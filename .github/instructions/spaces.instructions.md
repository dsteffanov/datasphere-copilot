---
description: "Use when creating spaces or managing space users in SAP Datasphere. Covers space create payload, user add/remove payload format, role prerequisites, storage/RAM sizing, and common pitfalls."
---
# Space Payload Reference

## Create Space Payload

The top-level key is the space technical ID. The space ID becomes the permanent identifier — it cannot be changed after creation.

```json
{
  "<SPACE_ID>": {
    "spaceDefinition": {
      "version": "1.0.4",
      "label": "<Display Name>",
      "assignedStorage": 2000000000,
      "assignedRam": 2000000000,
      "enableDataLake": false,
      "allowConsumption": false
    }
  }
}
```

### Storage & RAM Sizing

| User Says | Bytes |
|---|---|
| 1 GB | `1000000000` |
| 2 GB | `2000000000` |
| 4 GB | `4000000000` |
| 8 GB | `8000000000` |
| 16 GB | `16000000000` |

Default to 2 GB for both storage and RAM if the user doesn't specify.

### Optional Space Properties

| Property | Type | Default | Notes |
|---|---|---|---|
| `longDescription` | string | `""` | Free-text description |
| `enableDataLake` | boolean | `false` | Enable SAP HANA Cloud Data Lake |
| `allowConsumption` | boolean | `false` | Allow external consumption |
| `auditing` | object | — | Audit policy configuration |

## Add Users to a Space

**Payload format:**
```json
[
  {
    "id": "<USER_ID>",
    "roles": ["PROFILE:<package>:<role_name>"]
  }
]
```

### Critical Rules

1. **Use `id`, NOT `userName`** — the API schema requires `id` and rejects `userName` as an additional property
2. **Roles are strings, NOT objects** — `["PROFILE:t.CQ7TNL:Role_Name"]` not `[{"id": "..."}]`
3. **Minimum 2 properties** — each array entry needs both `id` and `roles`
4. **Role must be scoped to the target space** — otherwise the API returns a `roleIDValidation` error

### Role Pre-requisite Workflow

Before adding a user to a space, a scoped role must exist that includes that space as a scope:

```
Option A: Use an existing role (⚠ grants access to ALL users in that role)
  └─ datasphere scoped-roles scopes add --role <ROLE_ID> --scopes <SPACE>
  └─ datasphere spaces users add --space <SPACE> --file-path users.json

Option B: Create a dedicated role (✅ recommended for isolated access)
  └─ datasphere scoped-roles create --file-path role.json
  └─ datasphere scoped-roles scopes add --role <NEW_ROLE_ID> --scopes <SPACE>
  └─ datasphere scoped-roles users add --role <NEW_ROLE_ID> --file-path users.json
```

### Discovering Available Roles

To find which roles can be assigned:
```
datasphere scoped-roles list
```

To find the full role ID (needed for `--role` flag and user payloads):
```
datasphere scoped-roles read --role PROFILE:<package>:<role_name>
```

The role ID format is always: `PROFILE:<package>:<role_name>`
- Custom roles: `PROFILE:t.<TENANT_ID>:<role_name>` (e.g., `PROFILE:t.CQ7TNL:Sandbox_Admin`)
- Predefined roles: `PROFILE:sap.dwc:<role_name>` (e.g., `PROFILE:sap.dwc:Scoped_Data_Warehouse_Cloud_Modeler`)

## Read Users — Accept Headers

| MIME Type | Returns |
|---|---|
| `application/vnd.sap.datasphere.space.users.list+json` | Simple list of user IDs |
| `application/vnd.sap.datasphere.space.users.details+json` | Full details: userName, roles, displayName, isScopeAdmin |

Default (no `--accept`) returns the simple list.

## Verified Example — Add Single User

```json
[{"id": "O38648", "roles": ["PROFILE:t.CQ7TNL:Copilot_Admin"]}]
```

## Verified Example — Add Multiple Users

```json
[
  {"id": "USER1", "roles": ["PROFILE:t.CQ7TNL:Sandbox_Admin"]},
  {"id": "USER2", "roles": ["PROFILE:t.CQ7TNL:Sandbox_Admin"]}
]
```
