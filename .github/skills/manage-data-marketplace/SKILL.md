---
name: manage-data-marketplace
description: 'Use when managing data providers, data products, activation keys, licenses, releases, or contexts in the SAP Datasphere Data Marketplace.'
---

# Manage Data Marketplace

## Supported Intents
- List data providers
- Read a data provider
- Create a data provider
- Overwrite a data provider
- Update a data provider
- Generate activation keys for a provider
- List activation keys for a provider
- Delete activation keys for a provider
- List data products
- Read a data product
- List data products by provider
- List licenses by provider
- List/read/create/update data product releases
- List contexts by provider

## CLI Command Templates

### Data Providers
```
# List all data providers accessible to the current user
datasphere marketplace providers list
  [--accept "application/vnd.sap.marketplace.providers.list+json"
           |"application/vnd.sap.marketplace.providers.details+json"]

# Read a specific data provider
datasphere marketplace providers read
  --provider-identifier <uuid | contentAggregatorUUID:contentAggregatorProviderID>

# Create a new data provider
datasphere marketplace providers create
  --file-path <file>.json | --input <input>

# Overwrite ALL properties of a data provider
datasphere marketplace providers overwrite
  --provider-identifier <identifier>
  --file-path <file>.json | --input <input>

# Update selected properties of a data provider
datasphere marketplace providers update
  --provider-identifier <identifier>
  --file-path <file>.json | --input <input>
```

### Provider Activation Keys
```
# Generate activation keys for a provider
datasphere marketplace providers keys generate
  --provider-identifier <identifier>
  --file-path <file>.json | --input <input>

# List activation keys for a provider
datasphere marketplace providers keys list
  --provider-identifier <identifier>

# Delete activation keys for a provider
datasphere marketplace providers keys delete
  --provider-identifier <identifier>
  --file-path <file>.json | --input <input>
```

### Data Products
```
# List data products
datasphere marketplace products list

# Read a data product
datasphere marketplace products read
  --product-identifier <identifier>

# List data products by provider
datasphere marketplace products-by-provider list
  --provider-identifier <identifier>
```

### Licenses
```
# List licenses by provider
datasphere marketplace licenses-by-provider list
  --provider-identifier <identifier>
```

### Releases
```
# List releases for a product
datasphere marketplace releases list
  --provider-identifier <identifier>
  --product-identifier <identifier>

# Read a specific release
datasphere marketplace releases read
  --provider-identifier <identifier>
  --product-identifier <identifier>
  --release-identifier <identifier>

# Create a release
datasphere marketplace releases create
  --provider-identifier <identifier>
  --product-identifier <identifier>
  --file-path <file>.json | --input <input>

# Update a release
datasphere marketplace releases update
  --provider-identifier <identifier>
  --product-identifier <identifier>
  --release-identifier <identifier>
  --file-path <file>.json | --input <input>
```

### Contexts by Provider
```
# List contexts for a provider
datasphere marketplace contexts-by-provider list
  --provider-identifier <identifier>
```

## Provider Identifier Formats
- UUID only: `0b0dfe8e-f974-407d-8987-6d863b2c5e83`
- Content aggregator + provider: `<aggregatorUUID>:<contentAggregatorProviderID>`

## Data Provider Definition File Format (JSON)
```json
{
  "name": "<string>",
  "contentAggregatorProviderID": "<string>",
  "logo": "<base64-object>",
  "description": "<string>",
  "homepageUrl": "<string>",
  "linkedinUrl": "<string>",
  "regionalCoverages": ["Global", "Europe", "North America", ...],
  "dataCategories": ["<string>", ...],
  "industries": ["<string>", ...],
  "sapApplications": ["<string>", ...],
  "contactEmail": "<string>",
  "sapEmail": "<string>",
  "country_code": "<string>",
  "zipCode": "<string>",
  "city": "<string>",
  "address1": "<string>",
  "address2": "<string>",
  "phoneNumber": "<string>",
  "shipments": ["Direct|External|OpenSql"],
  "marketplaceVisibilities": ["public|private|internal"]
}
```
Max file size: 25MB. Logo must be Base64 encoded (bmp, svg+xml, gif, jpeg, png, tiff).

## Parameters
| Parameter | Required | Notes |
|-----------|----------|-------|
| --provider-identifier | Yes (most) | Provider UUID or contentAggregatorUUID[:providerID] |
| --product-identifier | Yes (products/releases) | Data product identifier |
| --release-identifier | Yes (releases read/update) | Release identifier |
| --file-path | Conditional | Path to JSON definition file |
| --input | Conditional | Inline input string |
| --accept | No | MIME type for response format |

## Required Role
- DW Modeler role (Data Warehouse Data Builder CRUD, Spaces -RU-----)
- Technical User OAuth client supported

## Safety
- `providers keys delete` is destructive — requires confirmation
- `providers overwrite` replaces ALL properties — use `update` for partial changes
- Managed providers require content aggregator profile membership

## Examples
- "List all data providers"
- "Read data provider with ID abc-123"
- "Create data provider from provider.json"
- "Update data provider abc-123 from updated.json"
- "Generate activation keys for provider abc-123"
- "List activation keys for provider abc-123"
- "Delete activation keys for provider abc-123"
- "List all data products"
- "List data products for provider abc-123"
- "List licenses for provider abc-123"
- "List releases for product xyz-456 in provider abc-123"
- "List contexts for provider abc-123"
