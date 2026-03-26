---
description: "Use when creating or updating SQL views in SAP Datasphere. Covers the @DataWarehouse.sqlEditor.query annotation, element mapping, SQL quoting rules, JOINs, WHERE clauses, parameters, and JSON payload structure for views CLI commands."
---
# SQL View Payload Reference

## Critical: What Makes a View "SQL"

The annotation `@DataWarehouse.sqlEditor.query` is what distinguishes an SQL view from a graphical (CDS) view. Without it, the view is treated as graphical and will likely fail to deploy if it also lacks a CDS `query` object.

**Default to SQL view** when the user says "create a view" without specifying the type.

## Structural Rules

- `kind` is always `"entity"` (never `"view"`)
- `@ObjectModel.supportedCapabilities` is a **single object** `{ "#": "..." }` — NOT an array
- `@DataWarehouse.sqlEditor.query` holds the raw SQL `SELECT` statement as a string
- `elements` must list every output column with its CDS type — these must match the SQL SELECT list
- Column names in the SQL string must be double-quoted: `\"Column_Name\"`
- Source table/view names in SQL must be double-quoted: `\"Source_Table\"`
- Cross-space sources use dot notation: `\"SPACE.Table_Name\"`

## SQL Quoting in JSON

Because the SQL is inside a JSON string, all double quotes must be escaped:

```json
"@DataWarehouse.sqlEditor.query": "SELECT \"Name\", \"Address\" FROM \"My_Table\""
```

## SQL Patterns

### Simple SELECT (all columns)
```sql
SELECT "Col1", "Col2", "Col3" FROM "Source_Table"
```
Always list columns explicitly — avoid `SELECT *`.

### With WHERE
```sql
SELECT "Name", "Status" FROM "Orders" WHERE "Status" = 'Active'
```

### With JOIN
```sql
SELECT A."Order_ID", A."Customer", B."Product_Name"
FROM "Orders" A
LEFT JOIN "Order_Items" B ON A."Order_ID" = B."Order_ID"
```

### With Alias
```sql
SELECT "First_Name" AS "Name", "Amount" * 1.1 AS "Amount_With_Tax" FROM "Customers"
```
When using aliases, the `elements` keys must match the alias names.

### Cross-Space Source
```sql
SELECT "Col1", "Col2" FROM "OTHER_SPACE.Source_Table"
```

### With Input Parameter
```sql
SELECT "Col1", "Col2" FROM "Source_Table" WHERE "Year" = :YEAR_PARAM
```
Input parameters use `:PARAM_NAME` syntax in SQL. They also require a `params` section in the JSON (see below).

## Input Parameters (Optional)

When the SQL uses `:PARAM_NAME`, add a `params` section to the definition:

```json
{
  "definitions": {
    "My_View": {
      "kind": "entity",
      "params": {
        "YEAR_PARAM": {
          "type": "cds.String",
          "length": 4
        }
      },
      "@DataWarehouse.sqlEditor.query": "SELECT \"Col1\" FROM \"Table\" WHERE \"Year\" = :YEAR_PARAM",
      ...
    }
  }
}
```

## Semantic Usage for Views

| Use Case | `modelingPattern` | `supportedCapabilities` | Extra Annotations |
|---|---|---|---|
| Generic / Relational | `DATA_STRUCTURE` | `DATA_STRUCTURE` | — |
| Dimension | `ANALYTICAL_DIMENSION` | `ANALYTICAL_DIMENSION` | Add `@Analytics.dimension: true` on each element |
| Fact | `ANALYTICAL_FACT` | `ANALYTICAL_FACT` | Mark measures with `@Analytics.Measure: true`, dimensions with `@Analytics.dimension: true` |

Default to `DATA_STRUCTURE` unless the user specifies dimension or fact semantics.

## Expose for Consumption

To make a view consumable by external tools (SAC, ODBC, etc.):
```json
"@DataWarehouse.consumption.external": true
```

## Element Annotations for Dimension Views

```json
"Column_Name": {
  "@EndUserText.label": "Column Label",
  "type": "cds.String",
  "length": 100,
  "@Analytics.dimension": true
}
```

## Verified Example — Simple SQL View

```json
{
  "definitions": {
    "Active_Customers": {
      "kind": "entity",
      "@EndUserText.label": "Active Customers",
      "@ObjectModel.modelingPattern": { "#": "DATA_STRUCTURE" },
      "@ObjectModel.supportedCapabilities": { "#": "DATA_STRUCTURE" },
      "@DataWarehouse.sqlEditor.query": "SELECT \"Name\", \"Address\", \"Status\" FROM \"Customers\" WHERE \"Status\" = 'Active'",
      "elements": {
        "Name": {
          "@EndUserText.label": "Name",
          "type": "cds.String",
          "length": 200
        },
        "Address": {
          "@EndUserText.label": "Address",
          "type": "cds.String",
          "length": 500
        },
        "Status": {
          "@EndUserText.label": "Status",
          "type": "cds.String",
          "length": 50
        }
      }
    }
  }
}
```

## Verified Example — Dimension View with Parameter

```json
{
  "definitions": {
    "Fiscal_Weeks": {
      "kind": "entity",
      "@EndUserText.label": "Fiscal Weeks",
      "@ObjectModel.modelingPattern": { "#": "ANALYTICAL_DIMENSION" },
      "@ObjectModel.supportedCapabilities": { "#": "ANALYTICAL_DIMENSION" },
      "@DataWarehouse.consumption.external": true,
      "params": {
        "FISCAL_YEAR": {
          "type": "cds.String",
          "length": 4
        }
      },
      "@DataWarehouse.sqlEditor.query": "SELECT A.\"Fiscal_Year\", A.\"Calendar_Date\", A.\"Fiscal_Period\" FROM \"DENIS.Fiscal_Date\" A WHERE A.\"Fiscal_Year\" = :FISCAL_YEAR",
      "elements": {
        "Fiscal_Year": {
          "@EndUserText.label": "Fiscal Year",
          "type": "cds.String",
          "length": 4,
          "key": true,
          "notNull": true,
          "@Analytics.dimension": true
        },
        "Calendar_Date": {
          "@EndUserText.label": "Calendar Date",
          "type": "cds.Date",
          "key": true,
          "notNull": true,
          "@Analytics.dimension": true
        },
        "Fiscal_Period": {
          "@EndUserText.label": "Fiscal Period",
          "type": "cds.String",
          "length": 3,
          "@Analytics.dimension": true
        }
      }
    }
  }
}
```
