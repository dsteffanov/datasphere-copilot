---
description: "Use when creating, updating, or managing scoped roles in SAP Datasphere. Covers role creation payload, inheritance templates, adding users with scopes, role ID format, and the workflow for granting isolated space access."
---
# Scoped Roles Payload Reference

## Create Scoped Role Payload

```json
{
  "name": "<Role_Name>",
  "description": "<Human-readable description>",
  "inheritance": "<Parent_Role_Template>"
}
```

### Inheritance Templates

| Template | Privileges |
|---|---|
| `Data_Warehouse_Cloud_Space_Administrator` | Full space admin: manage space settings, users, objects, and data |
| `Data_Warehouse_Cloud_Modeler` | Create/edit modeling objects (tables, views, flows) |
| `Data_Warehouse_Cloud_Integrator` | Manage connections, replication, and data integration |
| `Data_Warehouse_Cloud_Viewer` | Read-only access to space objects |
| `Data_Warehouse_Cloud_Extended_Viewer` | Extended read-only (includes data preview) |
| `Data_Warehouse_Cloud_Consumer` | Consume exposed data only |

## Role ID Format

All role operations use the composite ID: `PROFILE:<package>:<role_name>`

| Type | Pattern | Example |
|---|---|---|
| Custom | `PROFILE:t.<TENANT>:<name>` | `PROFILE:t.CQ7TNL:Copilot_Admin` |
| Predefined | `PROFILE:sap.dwc:<name>` | `PROFILE:sap.dwc:Scoped_Data_Warehouse_Cloud_Modeler` |

The `package` for custom roles is tenant-specific. Discover it by reading any existing custom role:
```
datasphere scoped-roles read --role PROFILE:t.<TENANT>:<role_name>
```

## Add Users to a Scoped Role

**Payload format:**
```json
[
  {
    "id": "<USER_ID>",
    "scopes": ["<SPACE_ID>"]
  }
]
```

### Critical Rules

1. **Must have `id` AND `scopes`** — minimum 2 properties required, API rejects fewer
2. **Do NOT include `roles`** — this endpoint is already scoped to a role via `--role` flag
3. **`scopes` is an array of space IDs** — the space must already be added to the role
4. **Payload differs from `spaces users add`** — that uses `{"id", "roles"}`, this uses `{"id", "scopes"}`

### Payload Comparison

| Command | Payload Properties | Example |
|---|---|---|
| `spaces users add --space X` | `id` + `roles` (string array) | `[{"id":"U1","roles":["PROFILE:t.CQ7TNL:Admin"]}]` |
| `scoped-roles users add --role R` | `id` + `scopes` (string array) | `[{"id":"U1","scopes":["COPILOT"]}]` |

These are NOT interchangeable — using the wrong format returns a schema validation error.

## Workflow: Grant Isolated Access to a New Space

When adding a user to a newly created space without affecting other users:

```
1. Create dedicated role
   datasphere scoped-roles create --file-path role.json
   Payload: {"name":"My_Admin","description":"...","inheritance":"Data_Warehouse_Cloud_Space_Administrator"}

2. Add space as scope
   datasphere scoped-roles scopes add --role PROFILE:t.<TENANT>:My_Admin --scopes MY_SPACE

3. Add user to role with scope
   datasphere scoped-roles users add --role PROFILE:t.<TENANT>:My_Admin --file-path users.json
   Payload: [{"id":"USER1","scopes":["MY_SPACE"]}]
```

> **Warning:** If you skip step 1 and add the space to an existing role instead, ALL users already assigned to that role automatically gain access to the new space.

## Verified Example — Full Isolated Access Setup

Role definition (`tmp/scoped-role-Copilot_Admin.json`):
```json
{
  "name": "Copilot_Admin",
  "description": "Administrator role for COPILOT space",
  "inheritance": "Data_Warehouse_Cloud_Space_Administrator"
}
```

User assignment (`tmp/role-Copilot_Admin-users.json`):
```json
[{"id": "<USER_ID>", "scopes": ["<SPACE_ID>"]}]
```

Commands executed:
```
datasphere scoped-roles create --file-path tmp/scoped-role-Copilot_Admin.json
datasphere scoped-roles scopes add --role PROFILE:t.CQ7TNL:Copilot_Admin --scopes COPILOT
datasphere scoped-roles users add --role PROFILE:t.CQ7TNL:Copilot_Admin --file-path tmp/role-Copilot_Admin-users.json
```
