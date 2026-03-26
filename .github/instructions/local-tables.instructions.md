---
description: "Use when creating or updating local tables in SAP Datasphere. Covers CDS column types, key columns, delta capture, and JSON payload structure for local-tables CLI commands."
---
# Local Table Payload Reference

## CDS Column Types

| User Says | CDS Type | Extra Properties |
|---|---|---|
| `INTEGER`, `INT` | `cds.Integer` | — |
| `BIGINT`, `INT64` | `cds.Integer64` | — |
| `STRING`, `NVARCHAR`, `VARCHAR` | `cds.String` | `"length": <n>` (default 200 if unspecified) |
| `BOOLEAN`, `BOOL` | `cds.Boolean` | — |
| `DATE` | `cds.Date` | — |
| `TIME` | `cds.Time` | — |
| `DATETIME`, `TIMESTAMP` | `cds.Timestamp` | — |
| `DECIMAL`, `DEC` | `cds.Decimal` | `"precision": <p>, "scale": <s>` |
| `DOUBLE`, `FLOAT` | `cds.Double` | — |
| `BINARY`, `VARBINARY` | `cds.Binary` | `"length": <n>` |
| `LARGESTRING`, `CLOB` | `cds.LargeString` | — |
| `LARGEBINARY`, `BLOB` | `cds.LargeBinary` | — |
| `TINYINT` | `cds.hana.TINYINT` | — |
| `SMALLINT` | `cds.hana.SMALLINT` | — |

## Key Column Rules

- At least one key column is recommended but not enforced by the API
- Key columns require both `"key": true` and `"notNull": true`
- Non-key columns: omit `key` and `notNull` entirely (don't set them to `false`)

## Default Length Values

When the user doesn't specify a length:
- `cds.String` → `200`
- Short identifiers (ID, Code, Status) → `100`
- Long text (Description, Address, Notes) → `500`
- Names → `200`

## Delta Capture Tables

When the user asks for a delta-capture-enabled table:
- Read with `--accept "application/vnd.sap.datasphere.object.content.design-time+json"`
- The delta capture configuration is part of the design-time payload — do not add it manually

## Structural Rules

- `kind` is always `"entity"`
- `@ObjectModel.supportedCapabilities` is an **array**: `[{ "#": "DATA_STRUCTURE" }]`
- Technical names: no spaces, use underscores (e.g., `My_Table`)
- `@EndUserText.label` is the business-friendly display name (spaces allowed)
- Each element needs `@EndUserText.label` and `type` at minimum

## Verified Example

```json
{
  "definitions": {
    "Orders": {
      "kind": "entity",
      "@EndUserText.label": "Orders",
      "@ObjectModel.modelingPattern": { "#": "DATA_STRUCTURE" },
      "@ObjectModel.supportedCapabilities": [{ "#": "DATA_STRUCTURE" }],
      "elements": {
        "Order_ID": {
          "@EndUserText.label": "Order ID",
          "type": "cds.Integer",
          "key": true,
          "notNull": true
        },
        "Customer_Name": {
          "@EndUserText.label": "Customer Name",
          "type": "cds.String",
          "length": 200
        },
        "Total": {
          "@EndUserText.label": "Total",
          "type": "cds.Decimal",
          "precision": 15,
          "scale": 2
        },
        "Order_Date": {
          "@EndUserText.label": "Order Date",
          "type": "cds.Date"
        },
        "Is_Completed": {
          "@EndUserText.label": "Is Completed",
          "type": "cds.Boolean"
        }
      }
    }
  }
}
```
