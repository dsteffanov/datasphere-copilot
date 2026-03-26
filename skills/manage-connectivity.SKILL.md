# Skill: Manage Connectivity

## Supported Intents
- List TLS certificates
- Upload/add a TLS certificate
- Delete a TLS certificate
- List connections in a space
- Read a connection definition
- Create a connection
- Validate a connection
- Edit/update a connection
- Delete a connection

## CLI Command Templates

### TLS Server Certificates
```
# List all TLS certificates
datasphere configuration certificates list

# Upload a TLS certificate
datasphere configuration certificates upload
  --description <description>
  --file-path <path>.pem|.crt|.cer | --input '<stringified-json>'

# Delete a TLS certificate
datasphere configuration certificates delete
  --fingerprint <fingerprint>
```

### Space Connections
```
# List connections in a space
datasphere spaces connections list
  --space <id>
  [--accept "application/vnd.sap.datasphere.space.connections.list+json"
           |"application/vnd.sap.datasphere.space.connections.details+json"]
  [--details]
  [--name]
  [--features]
  [--top <n>]     # Max 200, default 10
  [--skip <n>]    # Default 0
  [--output <file>.json]

# Read a connection's JSON definition (no credentials)
# Note: the CLI subcommand is 'get', not 'read'
datasphere spaces connections get
  --space <id>
  --name <name>
  [--output <file>.json]

# Create a connection
datasphere spaces connections create
  --space <id>
  --file-path <file>.json | --input '<stringified-json>'

# Validate a connection
datasphere spaces connections validate
  --space <id>
  --name <name>

# Edit/update a connection (note: CLI subcommand is 'edit', not 'update')
datasphere spaces connections edit
  --space <id>
  --name <name>
  --file-path <file>.json | --input '<stringified-json>'

# Delete a connection
datasphere spaces connections delete
  --space <id>
  --name <name>
  [--force]
```

## Supported Connection Types
| Connection Type | Type ID |
|----------------|----------|
| Amazon Athena | ATHENA |
| Amazon Redshift | REDSHIFT |
| Amazon S3 | S3 |
| Apache Kafka | KAFKA |
| Cloud Data Integration | CDI |
| Confluent | CONFLUENT |
| Generic JDBC | GENERICJDBC |
| Generic OData | ODATA |
| Generic SFTP | SFTP |
| Google BigQuery | BIGQUERY |
| Google Cloud Storage | GCS |
| HDFS | HDFS |
| Microsoft Azure Blob Storage | WASB |
| Microsoft Azure Data Lake Store Gen2 | ADL |
| Microsoft Azure SQL Database | AZURESQL |
| Microsoft SQL Server | MSSQL |
| Oracle | ORACLEDB |
| SAP ABAP | ABAP |
| SAP BW | SAPBW |
| SAP BW/4HANA Model Transfer | SAPBWMODELTRANSFER |
| SAP ECC | SAPECC |
| SAP HANA | HANA |
| SAP HANA Cloud Data Lake Files | HDL_FILES |
| SAP HANA Cloud Data Lake Relational Engine | HDLDB |
| SAP SuccessFactors | SAPSF |
| SAP S/4HANA Cloud | SAPS4HANACLOUD |
| SAP S/4HANA On-Premise | SAPS4HANAOP |

## Parameters
| Parameter | Required | Notes |
|-----------|----------|---------|
| --space | Yes (connections) | Space technical ID |
| --name | Yes (read/validate/update/delete) | Connection technical name |
| --file-path | Conditional | Path to JSON definition file |
| --input | Conditional | Inline stringified JSON |
| --output | No | Write output to file |
| --details | No | Include full connection details (no credentials) |
| --features | No | Include enabled/disabled feature flags |
| --top | No | Limit results (max 200) |
| --skip | No | Skip first N results |
| --description | Yes (cert upload) | Certificate description |
| --fingerprint | Yes (cert delete) | Certificate fingerprint |

## Required Role
- Administrator role: certificate management
- Integrator or Space Administrator role: connections (CRUD)

## Safety
- `connections delete` is destructive — requires confirmation
- `certificates delete` is destructive — requires confirmation
- Connection definitions never include credentials in read/list output

## Examples
- "List TLS certificates"
- "Upload TLS certificate from cert.pem with description SAP SuccessFactors"
- "Delete certificate with fingerprint AB:CD:EF"
- "List connections in space SALES"
- "Read connection MyHANAConn in space SALES"
- "Create connection in space SALES from conn.json"
- "Validate connection MyConn in space DEMO"
- "Update connection MyConn in space SALES"
- "Delete connection OldConn from space DEMO"

## Examples
- "Create a JDBC connection named salesdb"
- "Delete the old_ftp connection"