# Skill: Manage Spaces

## Supported Intents
- List all spaces
- Read/get a space definition
- Create a space
- Delete a space
- List users in a space
- Read users in a space
- Add users to a space
- Update users in a space
- Remove users from a space
- List database users in a space
- Create a database user
- Reset database user password
- Update a database user
- Delete a database user
- List workload priorities and statement limits
- Update workload priorities and statement limits

## CLI Command Templates

### Spaces — CRUD
```
# List all spaces
datasphere spaces list
  [--output <file>.json]

# Read a space definition
datasphere spaces read
  --space <id>
  [--output <file>.json]

# Create or update a space (from JSON file or inline input)
datasphere spaces create
  --file-path <file>.json | --input '<stringified-json>'

# Delete a space
datasphere spaces delete
  --space <id>
```

> **Note on utilization:** `spaces read` only returns **allocated** values (`assignedStorage`, `assignedRam`). Actual consumed/used storage and RAM are **not available via the CLI** — only accessible through the Datasphere UI (Space Management → monitoring panel) or the Datasphere REST API monitoring endpoints. Never infer actual usage from CLI output.

### Save Space (alternative to create — saves without overwriting)
```
datasphere spaces save
  --file-path <file>.json | --input '<stringified-json>'
  [--force-save]    # Force save of definitions
```

### Space Users — CRUD
```
# List users in a space (note: the CLI subcommand is 'read', not 'list')
datasphere spaces users read
  --space <id>
  [--accept "application/vnd.sap.datasphere.space.users.list+json"
           |"application/vnd.sap.datasphere.space.users.details+json"]
  [--output <file>.json]

# Add users to a space
datasphere spaces users add
  --space <id>
  --file-path <file>.json | --input '<stringified-json>'

# Update users in a space
datasphere spaces users update
  --space <id>
  --file-path <file>.json | --input '<stringified-json>'

# Remove users from a space
datasphere spaces users remove
  --space <id>
  --file-path <file>.json | --input '<stringified-json>'
```

### Database Users (HDI)
```
# List database users in a space
datasphere dbusers list
  --space <id>
  [--output <file>.json]

# Create a database user
datasphere dbusers create
  --space <id>
  --file-path <name>.json | --input '<stringified-json>'
  [--output <file>.json]

# Reset database user password
datasphere dbusers password reset
  --space <id>
  --databaseuser <id>
  [--output <file>.json]

# Update a database user
datasphere dbusers update
  --space <id>
  --databaseuser <id>
  --file-path <name>.json | --input '<stringified-json>'
  [--output <file>.json]

# Delete a database user
datasphere dbusers delete
  --space <id>
  --databaseuser <id>
  [--force]
```

### Workload Priorities and Statement Limits
```
# List workload priorities
datasphere workload list
  [--output <file>.json]

# Update workload priorities
datasphere workload update
  --file-path <file>.json | --input '<stringified-json>'
```

## Parameters
| Parameter | Required | Notes |
|-----------|----------|---------|
| --space | Yes (most) | Space technical ID |
| --file-path | Conditional | Path to JSON definition file |
| --input | Conditional | Inline stringified JSON (alternative to --file-path) |
| --output | No | Path to write output JSON; omit to print to console |
| --accept | No | MIME type for response format |
| --databaseuser | Yes (dbusers) | Database user ID |

## JSON Payload Templates

When creating spaces or managing users, use these verified templates.

### Create Space

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

Key rules:
- The top-level key is the space technical ID (e.g., `"COPILOT"`)
- `assignedStorage` and `assignedRam` are in bytes (2000000000 = ~2 GB)
- `version` should be `"1.0.4"`

### Add Users to a Space

```json
[
  {
    "id": "<USER_ID>",
    "roles": ["PROFILE:<package>:<role_name>"]
  }
]
```

**CRITICAL rules:**
- Use `"id"`, NOT `"userName"` — the API rejects `userName` as an additional property
- `roles` is an array of **strings**, NOT objects — e.g., `["PROFILE:t.CQ7TNL:Sandbox_Admin"]`
- Each entry must have at least 2 properties (`id` + `roles`)
- The role must already be scoped to the target space, otherwise the API returns `roleIDValidation` error

> **Pre-requisite:** Before adding a user to a new space, ensure a scoped role with that space as a scope exists. If not, either:
> 1. Add the space as a scope to an existing role (`scoped-roles scopes add`), OR
> 2. Create a new dedicated role, add the space as scope, then add the user
>
> **Warning:** Adding a space as a scope to an existing role grants access to ALL users already assigned to that role. If you want only specific users, create a dedicated role.

### Remove Users from a Space

Same payload format as add:
```json
[
  {"id": "<USER_ID>", "roles": ["PROFILE:<package>:<role_name>"]}
]
```

See `.github/instructions/spaces.instructions.md` for more details on space payloads and user management patterns.

## Required Role
- Administrator role: create/read/update/delete spaces
- Space Administrator role: manage space properties, users, and HDI database users

## Safety
- `spaces delete` is destructive — requires confirmation
- `dbusers delete` is destructive — requires confirmation
- `spaces users remove` is destructive — requires confirmation

## Examples
- "List all spaces"
- "Read space SALES"
- "Create a space called COPILOT"
- "Create a space from sales_space.json"
- "Delete space OLD_SPACE"
- "List users in space MARKETING"
- "Add user O38648 to space COPILOT"
- "Add users to space SALES from users.json"
- "Remove users from space DEMO"
- "List database users in space DEV"
- "Reset password for database user DBUSER1 in space SALES"
- "Show workload priorities"
- "Update workload limits from update.json"