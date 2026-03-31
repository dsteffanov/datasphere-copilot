---
name: manage-scoped-roles
description: 'Use when listing, creating, updating, or deleting scoped roles in SAP Datasphere. Also covers adding/removing users and spaces (scopes) to scoped roles, and the workflow for granting isolated space access.'
---

# Manage Scoped Roles

## Supported Intents
- List scoped roles
- Read a scoped role definition
- Create a scoped role
- Update a scoped role
- Delete a scoped role
- List users assigned to a scoped role
- Add users to a scoped role
- Remove users from a scoped role
- List spaces assigned to a scoped role
- Add spaces to a scoped role
- Remove spaces from a scoped role

## CLI Command Templates

### List Scoped Roles
```
datasphere scoped-roles list
  [--output <file>.json]
```

### Read a Scoped Role
```
datasphere scoped-roles read
  --role <id>
  [--output <file>.json]
```

### Create a Scoped Role
```
datasphere scoped-roles create
  --file-path <file>.json | --input '<stringified-json>'
```

### Update a Scoped Role
```
datasphere scoped-roles update
  --role <id>
  --file-path <file>.json | --input '<stringified-json>'
```

### Delete a Scoped Role
```
datasphere scoped-roles delete
  --role <id>
  [--force]
```

### Manage Users in a Scoped Role
```
# List users assigned to a scoped role (note: subcommand is 'read', flag is '--role')
datasphere scoped-roles users read
  --role <id>
  [--output <file>.json]

# Add users to a scoped role
datasphere scoped-roles users add
  --role <id>
  --file-path <file>.json | --input '<stringified-json>'

# Remove users from a scoped role
datasphere scoped-roles users remove
  --role <id>
  --file-path <file>.json | --input '<stringified-json>'
```

### Manage Scopes (Spaces) in a Scoped Role
> **Note:** The CLI subcommand is `scopes`, not `spaces`.
```
# List scopes assigned to a scoped role
datasphere scoped-roles scopes read
  --role <id>
  [--output <file>.json]

# Add scopes to a scoped role
datasphere scoped-roles scopes add
  --role <id>
  --scopes <space_id>[,<space_id>,...]

# Remove scopes from a scoped role
datasphere scoped-roles scopes remove
  --role <id>
  --scopes <space_id>[,<space_id>,...]
```

## Parameters
| Parameter | Required | Notes |
|---|---|---|
| `--role` | Yes (most commands) | Technical ID of the scoped role |
| `--file-path` | Conditional | Path to JSON role definition or user/space list file |
| `--input` | Conditional | Stringified JSON (alternative to `--file-path`) |
| `--output` | No | Path to write JSON output to file |

## JSON Payload Templates

When creating roles or managing users, use these verified templates.

### Create Scoped Role

```json
{
  "name": "<Role_Name>",
  "description": "<Description>",
  "inheritance": "<Parent_Role_Template>"
}
```

Common `inheritance` values:
| Template | Privileges |
|---|---|
| `Data_Warehouse_Cloud_Space_Administrator` | Full space admin |
| `Data_Warehouse_Cloud_Modeler` | Modeling privileges |
| `Data_Warehouse_Cloud_Integrator` | Integration privileges |
| `Data_Warehouse_Cloud_Viewer` | Read-only |
| `Data_Warehouse_Cloud_Consumer` | Consumption only |
| `Data_Warehouse_Cloud_Extended_Viewer` | Extended read-only |

### Add Users to a Scoped Role

```json
[
  {
    "id": "<USER_ID>",
    "scopes": ["<SPACE_ID>"]
  }
]
```

**CRITICAL rules:**
- Each entry must have exactly `"id"` and `"scopes"` — minimum 2 properties required
- `scopes` is an array of space IDs (strings)
- The space must already be added as a scope to the role (`scoped-roles scopes add`) before assigning users
- Do NOT include `roles` property — this endpoint only takes `id` + `scopes`

### Remove Users from a Scoped Role

Same payload format as add:
```json
[
  {"id": "<USER_ID>", "scopes": ["<SPACE_ID>"]}
]
```

### Role ID Format

The `--role` flag requires the full composite ID: `PROFILE:<package>:<role_name>`

Examples:
- `PROFILE:t.CQ7TNL:Sandbox_Admin`
- `PROFILE:t.CQ7TNL:Copilot_Admin`
- `PROFILE:sap.dwc:Scoped_Data_Warehouse_Cloud_Modeler` (predefined)

The `package` value is tenant-specific. Read an existing role to discover the correct package for custom roles.

## Workflow: Add a User to a New Space

When a user asks to add someone to a space and no suitable role exists:

1. **Create a dedicated scoped role** → `scoped-roles create`
2. **Add the space as a scope** → `scoped-roles scopes add --role <id> --scopes <space>`
3. **Add the user to the role with scope** → `scoped-roles users add --role <id>` with payload `[{"id":"<user>","scopes":["<space>"]}]`

> **Warning:** If you add a space to an existing role instead, ALL users already assigned to that role gain access to the new space.

See `.github/instructions/scoped-roles.instructions.md` for more details.

## Safety
- Delete is destructive — always confirm before executing
- Requires DW Administrator or equivalent global role privileges
- Adding a scope to an existing role affects all users in that role

## Examples
- "List all scoped roles"
- "Read scoped role DW_MODELER_ROLE"
- "Create a scoped role Copilot_Admin for space COPILOT"
- "Add user JSMITH to scoped role Copilot_Admin for space COPILOT"
- "Add space COPILOT to scoped role Sandbox_Admin"
- "Remove space SANDBOX from scoped role ANALYST_ROLE"
- "Delete scoped role OLD_ROLE"
