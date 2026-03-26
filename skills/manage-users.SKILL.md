# Skill: Manage Users

## Supported Intents
- List all users
- Create users
- Update users
- Delete users

> For global role operations, see `manage-global-roles.SKILL.md`.
> For scoped role operations, see `manage-scoped-roles.SKILL.md`.

## CLI Command Templates

### Users
```
# List all users
datasphere users list
  [--accept "application/vnd.sap.datasphere.space.users.list+json"
           |"application/vnd.sap.datasphere.space.users.details+json"]
  [--output <file>.json]

# Create users
datasphere users create
  --file-path <file>.json | --input '<stringified-json>'

# Update users
datasphere users update
  --file-path <file>.json | --input '<stringified-json>'

# Delete users
datasphere users delete
  --users <ID[,ID...]>
  [--force]
```

## Parameters
| Parameter | Required | Notes |
|-----------|----------|-------|
| --users | Yes (delete) | Comma-separated user IDs |
| --file-path | Conditional | Path to JSON definition file |
| --input | Conditional | Inline stringified JSON |
| --accept | No | MIME type for response format |
| --output | No | Write output to file |

## Required Role
- Administrator role required for all user management operations

## Safety
- `users delete` is destructive — requires confirmation

## Examples
- "List all users"
- "Create users from users.json"
- "Update user john.doe@company.com"
- "Delete user old.employee@company.com"
