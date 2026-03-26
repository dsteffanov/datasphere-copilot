# Skill: Manage Global Roles

## Supported Intents
- List global roles
- List users assigned to a global role
- Add users to a global role
- Remove users from a global role

> **Note:** The CLI only supports `list` for global roles themselves. There are no `read`, `create`, `update`, or `delete` commands for global roles. User assignment is managed via the `users` subcommand.

## CLI Command Templates

### List Global Roles
```
datasphere global-roles list
```

### Manage Users in a Global Role
```
# List users assigned to a global role
datasphere global-roles users list
  --role <id>

# Add users to a global role
datasphere global-roles users add
  --role <id>
  --users <user_id>[,<user_id>,...]

# Remove users from a global role
datasphere global-roles users remove
  --role <id>
  --users <user_id>[,<user_id>,...]
```

## Parameters
| Parameter | Required | Notes |
|---|---|---|
| `--role` | Yes (users commands) | Technical ID of the global role |
| `--users` | Yes (add/remove) | Comma-separated user IDs |

## Safety
- `global-roles users remove` removes role assignments — requires confirmation
- Requires a global role that grants System Information privileges (DW Administrator template)

## Examples
- "List all global roles"
- "List users in global role DW_Administrator"
- "Add user O38648 to global role DW_Modeler"
- "Remove user O57187 from global role DW_Consumer"
