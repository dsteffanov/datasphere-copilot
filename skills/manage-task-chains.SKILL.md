# Skill: Manage Task Chains and Tasks

## Supported Intents
- Run a task chain
- Run a replication flow
- Stop a replication flow
- Get status of a replication flow or object
- Restart an object in a replication flow
- List task logs
- Get task log status or details
- Get extended task log details
- Give consent for scheduled tasks
- Revoke consent for scheduled tasks
- Get consent status

## CLI Command Templates

### Task Chains
```
# Run a task chain (returns task ID)
datasphere tasks chains run
  --space <id>
  --object <technical_name>
  [--input-parameters '<stringified-json>']

# Example with input parameters:
datasphere tasks chains run
  --space MySpace
  --object LoadChain
  --input-parameters '{"YEAR":"2026","DEPT":"Sales"}'
```

### Replication Flows
```
# Run a replication flow
datasphere tasks replication-flows run
  --space <id>
  --technical-name <technical_name>

# Stop a replication flow
datasphere tasks replication-flows stop
  --space <id>
  --technical-name <technical_name>

# Get status of a replication flow (or a specific object within it)
datasphere tasks replication-flows status
  --space <id>
  --technical-name <technical_name>
  [--object <id>]

# Restart an object within a replication flow
datasphere tasks replication-flows restart-object
  --space <id>
  --technical-name <technical_name>
  --object <id>
```

### Task Logs
```
# List all logs for an object (all runs)
# Note: the flag is --objectname, not --object
datasphere tasks logs list
  --space <id>
  --objectname <technical_name>

# Get status or details of a specific run
datasphere tasks logs get
  --space <id>
  --log-id <id>
  [--info-level status|details]    # default: status

# Get extended details of a specific run
datasphere tasks logs get-extended
  --space <id>
  --log-id <id>
  [--accept "application/vnd.sap.datasphere.task.log.status+json"
           |"application/vnd.sap.datasphere.task.log.status.object+json"
           |"application/vnd.sap.datasphere.task.log.details+json"
           |"application/vnd.sap.datasphere.task.log.details.extended+json"]
```

### Scheduled Task Consent
```
# Give consent for SAP Datasphere to run scheduled tasks on your behalf
datasphere tasks consent give [--verbose]

# Revoke consent
datasphere tasks consent revoke [--verbose]

# Get current consent status
datasphere tasks consent get
```

## Parameters
| Parameter | Required | Notes |
|-----------|----------|-------|
| --space | Yes | Space technical ID |
| --object | Yes (chains/logs) | Technical name of task chain or object |
| --technical-name | Yes (replication flows) | Technical name of replication flow |
| --input-parameters | No | JSON string of input parameter values |
| --log-id | Yes (logs get) | Log ID from `logs list` |
| --info-level | No | `status` (default) or `details` |
| --accept | No | MIME type for response format |
| --verbose | No | Print detailed response codes |

## Info Levels for `logs get`
| Level | Returns |
|-------|---------|
| `status` | RUNNING, COMPLETED, or FAILED |
| `details` | Full status including subtask logs |

## Consent Status Responses
| Command | Response |
|---------|----------|
| `give` | 201 (created) or 400 (already given) |
| `revoke` | 200 (revoked) or 404 (not found) |
| `get` | GIVEN + expiry date, or EXPIRED |

## Required Role
- Integrator scoped role (Data Warehouse Data Integration -RU-----)
- Technical User OAuth client supported

## Safety
- `replication-flows stop` interrupts running flows — requires confirmation
- `tasks consent revoke` removes automation consent — requires confirmation

## Examples
- "Run task chain LoadChain in space SALES"
- "Run task chain with parameters year=2026 and dept=Sales"
- "Run replication flow RepSales in space SALES"
- "Stop replication flow RepSales"
- "Get status of replication flow RepSales"
- "Restart object SalesHeader in replication flow RepSales"
- "List task logs for LoadChain in space SALES"
- "Get log details for log ID abc123"
- "Give consent for scheduled tasks"
- "Get consent status"