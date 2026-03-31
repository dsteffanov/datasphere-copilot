---
name: manage-modelling-objects
description: 'Use when listing, creating, reading, updating, deploying, or deleting SAP Datasphere modelling objects: local tables, views, data flows, replication flows, transformation flows, task chains, analytic models, business entities, fact models, consumption models, data access controls, ER models, intelligent lookups, and remote tables.'
---

# Manage Modelling Objects

## Supported Intents
- List objects of a type in a space
- Read an object definition
- Create an object (local table, view, data flow, replication flow, transformation flow, task chain, analytic model, business entity, fact model, consumption model, data access control, er-model, intelligent lookup, remote table)
- Update an object
- Delete an object
- Deploy an object
- Share an object to another space
- Modify metadata translations of an object

## CLI Command Templates

### List Objects

> **CRITICAL — Always include the object type.** The command `datasphere objects list` does NOT exist and will error. The correct form is always `datasphere objects <object-type> list`. Example: `datasphere objects local-tables list --space DENIS`. Never omit the object type segment.

```
datasphere objects <object-type> list
  --space <id>
  [--technical-names <name>[,...]]   # Filter by specific technical name(s)
  [--output <file>.json]
  [--select "<property>[,<property>...]"]
  [--filter "<property> <op> <value> [and|or ...]"]
  [--top <n>]    # Max 200, default 25
  [--skip <n>]
```

### Read an Object
```
datasphere objects <object-type> read
  --space <id>
  --technical-name <name>
  [--output <file>.json]             # omit to print to console; use --output alone to write to defaultFileName
  [--accept "application/vnd.sap.datasphere.object.content+json"              # default — most complete
           |"application/vnd.sap.datasphere.object.content.design-time+json"  # design-time, required for delta capture tables
           |"application/vnd.sap.datasphere.object.content.run-time+json"]    # runtime only
```
> **Note:** For local tables with delta capture enabled, always use `--accept application/vnd.sap.datasphere.object.content.design-time+json`

### Create an Object
```
datasphere objects <object-type> create
  --space <id>
  --file-path <file>.json
  [--allow-missing-dependencies]   # Allow save even if dependencies are missing
  [--custom-validation-options allowRevertReleaseState:true]
  [--save-anyway]     # Save even with validation warnings
  [--no-deploy]       # Save without deploying
  [--verbose]    # Shows HTTP request/response — always add when debugging failures
```
> **Note:** The technical name is defined inside the JSON payload, not as a CLI flag on create.

> **Always use `--file-path`, not `--input`.** The `--input` flag is unreliable in PowerShell due to JSON escaping issues between PowerShell and the CLI's Node.js argument parser. Always write JSON to a file in `tmp/` and use `--file-path`.

> **Always add `--verbose` on create.** Without it, failures produce zero diagnostic info (just "Failed to create an object from a JSON file or input string"). With `--verbose`, the full HTTP URL, status code, and error body are printed.

> **Pre-requisite — Space must have a user with a scoped role.** If the target space was just created, object create/update/delete will fail (often with opaque TLS or connection errors, not clear permission messages). Before creating objects in a new space, ensure a scoped role exists for that space and the current user is assigned to it. Use the `manage-spaces` and `manage-scoped-roles` skills to set this up.

> **Schema Bootstrap:** If the payload structure is unclear or the command fails with a schema/validation error, find an existing object of the same type in the same space, read its definition, and use it as a structural reference to build or correct the payload.

> **Object already exists?** If the error says "An object with this name already exists. To update it, use the update command", switch to `objects <type> update --space <id> --technical-name <name> --file-path <file>.json` instead of re-creating.

### Update an Object
```
datasphere objects <object-type> update
  --space <id>
  --technical-name <name>
  --file-path <absolute-path-to-file>.json
  [--custom-validation-options allowRevertReleaseState:true]
  [--save-anyway]
  [--no-deploy]
  [--allow-missing-dependencies]   # Allow save even if dependencies are missing
  [--verbose]    # Shows HTTP request/response — always add when debugging failures
```

> **CRITICAL — Always use absolute paths with `--file-path` on update.** Relative paths silently fail with "Failed to read content from file". Use the full workspace-rooted path, e.g. `C:\Users\...\tmp\Fiscal_Weeks.json`.

> **Validate JSON before submitting.** Run `node -e "JSON.parse(require('fs').readFileSync('tmp/file.json','utf8')); console.log('valid')"` before every update. Patching a JSON file can introduce duplicate keys that break parsing silently in some tools (PowerShell's `ConvertFrom-Json`) but are caught correctly by Node.js.

> **Use `--verbose` to diagnose failures.** It prints the full HTTP URL, request, and response. An SSL fallback warning (`fallback to TLSv1.2`) is harmless — the operation still succeeds if the response is `200 OK` or `204 No Content`.

> **TLS retry — don't panic on EPROTO.** The CLI may log `write EPROTO ... ssl3_read_bytes ... tlsv1 alert protocol version` on every write (POST/PUT). This is the initial TLS 1.3 handshake failing before the CLI retries with TLS 1.2. If the next line shows `200 OK (fallback to TLSv1.2)` or `204 No Content (fallback to TLSv1.2)`, the operation succeeded. If the fallback itself fails, retry the command once — transient network conditions can prevent the fallback from triggering.

### Delete an Object
```
datasphere objects <object-type> delete
  --space <id>
  --technical-name <name>
  [--delete-anyway]   # Force deletion even if other objects depend on it
  [--force]           # Force command execution without confirmation
```

> **Note:** There is no standalone `deploy` command. Deployment happens automatically on create/update unless `--no-deploy` is specified.

### Modify Sharing (cross-space sharing)
```
datasphere objects <object-type> update
  --space <id>
  --technical-name <name>
  --input '<json with sharing definition>'
```
> Sharing is defined inside the object definition JSON under the `sharing` property.

### Modify Metadata Translations
```
datasphere objects <object-type> update
  --space <id>
  --technical-name <name>
  --input '<json with translations>'
```
> Translations are defined inside the object definition JSON under the `translations` property.

## Available Object Types
| Object Type | CLI Type ID | Can Create/Update via CLI | Notes |
|---|---|---|---|
| Remote Tables | `remote-tables` | No | Imported from sources |
| Local Tables | `local-tables` | Yes | Use `--accept design-time` for delta capture |
| Views | `views` | Yes | Graphical or SQL views |
| Intelligent Lookups | `intelligent-lookups` | Yes | |
| Entity-Relationship Models | `er-models` | Yes | |
| Types | `types` | No | Auto-imported with remote tables/content packages |
| Contexts | `contexts` | No | Auto-imported with remote tables/content packages |
| Data Flows | `data-flows` | Yes | |
| Replication Flows | `replication-flows` | Yes | |
| Transformation Flows | `transformation-flows` | Yes | |
| Task Chains | `task-chains` | Yes | |
| Analytic Models | `analytic-models` | Yes | |
| Business Entities | `business-entities` | Yes | |
| Fact Models | `fact-models` | Yes | |
| Consumption Models | `consumption-models` | Yes | |
| Data Access Controls | `data-access-controls` | Yes | |

## Filter Properties and OData Operators
| Operator | Meaning |
|---|---|
| `eq` | Equal to |
| `ne` | Not equal to |
| `gt` | Greater than |
| `lt` | Less than |
| `ge` | Greater than or equal to |
| `le` | Less than or equal to |

| Property | Filter Values / Format |
|---|---|
| `technicalName` | Object technical name |
| `businessName` | Object business name |
| `type` | `LocalTable`, `RemoteTable`, `View`, `IntelligentLookup`, `ReplicationFlow`, `TransformationFlow`, `DataFlow`, `TaskChain`, `ERModel`, `AnalyticModel`, `DataAccessControl`, `BusinessEntity`, `FactModel`, `ConsumptionModel`, `Folder` |
| `semanticUsage` | `Fact`, `RelationalDataset`, `Dimension`, `Hierarchy`, `HierarchyWithDirectory`, `Text`, `AnalyticalDataset` |
| `status` | `NotDeployed`, `Deployed`, `ChangesToDeploy`, `DesignTimeError`, `PendingDeployment` |
| `createdOn`, `changedOn`, `deployedOn` | Date: `YYYY-MM-DD` |
| `createdBy`, `changedBy` | Username |

Filter examples:
```
--filter "deployedOn gt '2022-12-31' and (semanticUsage eq Fact or semanticUsage eq Dimension)"
--filter "type eq View"
--filter "status eq Deployed"
--filter "changedBy eq Lisa"
--filter "technicalName eq Table_1 or technicalName eq Table_2"
```

## Select Properties
`technicalName`, `businessName`, `type`, `semanticUsage`, `status`, `createdOn`, `changedOn`, `deployedOn`, `createdBy`, `changedBy`, `defaultFileName`

## Parameters
| Parameter | Required | Notes |
|---|---|---|
| `--space` | Yes | Space technical ID |
| `--technical-name` | Yes (read/create/update/delete/deploy) | Object technical name |
| `--file-path` | Conditional | Path to JSON definition file |
| `--input` | Conditional | Stringified JSON (alternative to `--file-path`) |
| `--output` | No | Write output to file; use alone for defaultFileName |
| `--accept` | No | Response format for read |
| `--no-deploy` | No | Save without deploying |
| `--save-anyway` | No | Save despite validation warnings |
| `--custom-validation-options` | No | e.g. `allowRevertReleaseState:true` |
| `--top` | No | Max results (max 200, default 25) |
| `--skip` | No | Skip first N results |
| `--select` | No | Comma-separated list of properties to return |
| `--filter` | No | OData filter expression |
| `--technical-names` | No | Comma-separated list for filtering list results |

## Tmp Folder Usage
Always write output and definition files to `tmp/` folder:
- Read output: `--output tmp/<object-type>-<name>.json`
- Create/Update input: `--file-path tmp/<object-type>-<name>.json`

## Required Role
- DW Modeler scoped role (Space Files CRUD, Data Warehouse Data Builder CRUD, Business Builder CRUD)
- Technical User OAuth client supported

## Safety
- `delete` is destructive — always confirm before executing
- Cannot manage connections, folders, or packages via `objects` commands
- Cannot assign objects to folders or packages via create/update
- `types` and `contexts` cannot be created manually — they are auto-imported

## Examples
- "List all views in space SALES"
- "List local tables in space DENIS"
- "List deployed data flows in space DEV"
- "List objects changed by Lisa in space MARKETING"
- "List objects deployed after 2024-01-01 in space SALES"
- "Read view SalesView in space SALES"
- "Read local table MyTable with delta capture in space DEMO"
- "Create local table T1 in space DEMO from file tmp/local-tables-T1.json"
- "Create a view SalesView in space SALES from file tmp/views-SalesView.json"
- "Update analytic model SalesModel in space MARKETING"
- "Deploy replication flow RepFlow in space DEV"
- "Delete view OldView in space DEMO"
- "Share local table Products from space SALES to space REPORTING"

## JSON Payload Templates

When creating objects, use these verified templates. The JSON structure varies by object type — **do not guess**. If a template is not listed here, read an existing deployed object of the same type and use it as a reference (Schema Bootstrap).

### Local Table

```json
{
  "definitions": {
    "<TECHNICAL_NAME>": {
      "kind": "entity",
      "@EndUserText.label": "<Business Name>",
      "@ObjectModel.modelingPattern": { "#": "DATA_STRUCTURE" },
      "@ObjectModel.supportedCapabilities": [{ "#": "DATA_STRUCTURE" }],
      "elements": {
        "<COL_NAME>": {
          "@EndUserText.label": "<Column Label>",
          "type": "<cds_type>",
          "key": true,          
          "notNull": true       
        }
      }
    }
  }
}
```

Key rules:
- `kind` is always `"entity"` (never `"table"`)
- `@ObjectModel.supportedCapabilities` is an **array** `[{ "#": "..." }]`
- At least one column should have `"key": true, "notNull": true` for best practice
- `key` and `notNull` are optional per column — omit them for non-key nullable columns
- See `.github/instructions/local-tables.instructions.md` for column type reference

### SQL View

```json
{
  "definitions": {
    "<TECHNICAL_NAME>": {
      "kind": "entity",
      "@EndUserText.label": "<Business Name>",
      "@ObjectModel.modelingPattern": { "#": "DATA_STRUCTURE" },
      "@ObjectModel.supportedCapabilities": { "#": "DATA_STRUCTURE" },
      "@DataWarehouse.sqlEditor.query": "SELECT <columns> FROM \"<source_table>\"",
      "elements": {
        "<COL_NAME>": {
          "@EndUserText.label": "<Column Label>",
          "type": "<cds_type>"
        }
      }
    }
  }
}
```

Key rules:
- `kind` is `"entity"` (never `"view"`)
- `@ObjectModel.supportedCapabilities` is a **single object** `{ "#": "..." }` (not an array)
- **`@DataWarehouse.sqlEditor.query`** contains the raw SQL string — this is what makes it an SQL view
- Column names in SQL must be double-quoted: `"SELECT \"Col1\", \"Col2\" FROM \"Table\""`
- `elements` must list every output column with matching types from the source
- Without `@DataWarehouse.sqlEditor.query` the view saves as graphical (CDS) — it won't contain SQL and may fail to deploy
- See `.github/instructions/sql-views.instructions.md` for SQL patterns and annotation reference

### Graphical View (CDS)

```json
{
  "definitions": {
    "<TECHNICAL_NAME>": {
      "kind": "entity",
      "@EndUserText.label": "<Business Name>",
      "@ObjectModel.modelingPattern": { "#": "DATA_STRUCTURE" },
      "@ObjectModel.supportedCapabilities": [{ "#": "DATA_STRUCTURE" }],
      "query": {
        "SELECT": {
          "from": { "ref": ["<source_entity>"] },
          "columns": [
            { "ref": ["<col1>"] },
            { "ref": ["<col2>"] }
          ]
        }
      }
    }
  }
}
```

Key rules:
- No `@DataWarehouse.sqlEditor.query` — uses CDS `query` object instead
- `@ObjectModel.supportedCapabilities` is an **array** `[{ "#": "..." }]`
- Use this only when the user explicitly asks for a graphical or CDS view
- Default to SQL view when the user says "create a view" without specifying type

### Semantic Usage Patterns

| Use Case | `modelingPattern` | `supportedCapabilities` |
|---|---|---|
| Generic / Relational | `DATA_STRUCTURE` | `DATA_STRUCTURE` |
| Dimension | `ANALYTICAL_DIMENSION` | `ANALYTICAL_DIMENSION` |
| Fact / Analytical Dataset | `ANALYTICAL_FACT` | `ANALYTICAL_FACT` |

Default to `DATA_STRUCTURE` unless the user specifies dimension or fact semantics.

## Agent Usage Examples (natural language)
- "List all views in space SALES"
- "List all local tables in space DEMO"
- "List all task chains in space DENIS"
- "List all replication flows in space SALES"
- "Read view SalesView in space SALES"
- "Read local table MyTable in space DEMO"
- "Show replication flow RepFlow in space DEV"
- "Create a table XDST in space DENIS"                  → generates minimal template
- "Create a view SalesView in space SALES"              → generates minimal template
- "Create a local table XDST in space DENIS from file table.json"
- "Create a view SalesView in space SALES from file view.json"
- "Update table XDST in space DENIS from file table.json"
- "Delete table OldTable in space DEMO"
- "Delete view SalesView in space SALES"

## Notes on Create
- No file: the agent generates a minimal inline JSON for `local-tables` and `views`.
- For all other types, use: `create <type> <name> in space <space> from file <file>.json`
- Add `--no-deploy` or `--save-anyway` manually in the CLI if needed.
