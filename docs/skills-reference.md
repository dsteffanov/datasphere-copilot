# Skills Reference

The agent loads skill files automatically based on your request. This page describes what each skill covers and provides example prompts you can use directly in Copilot Chat.

---

## manage-log-in

Handles authentication, host configuration, and token management.

**Example prompts:**
- `"Log in to the dev tenant"`
- `"Log out"`
- `"Show the current host"`
- `"Set the host to https://my-tenant.us21.hcs.cloud.sap"`
- `"Show stored secrets"`
- `"Initialize the CLI cache"`

---

## manage-spaces

Manages Datasphere spaces and their users, database users, and workload settings.

**Example prompts:**
- `"List all spaces"`
- `"Read the definition of space FINANCE"`
- `"Create a new space called SANDBOX with 4 GB memory and 2 vCPUs"`
- `"Delete space TESTSPACE"`
- `"List users in space DENIS"`
- `"Add user john.doe@company.com to space DENIS"`
- `"Create a database user REPORTING in space DENIS"`
- `"Reset the password for database user REPORTING in space DENIS"`
- `"Show workload priorities for space DENIS"`

---

## manage-connectivity

Manages TLS certificates and data source connections.

**Example prompts:**
- `"List all TLS certificates"`
- `"Upload TLS certificate from file my-cert.pem"`
- `"List connections in space DENIS"`
- `"Read the definition of connection S4_PROD in space DENIS"`
- `"Create a connection in space DENIS from file tmp/connection.json"`
- `"Validate connection S4_PROD in space DENIS"`
- `"Delete connection OLD_CONN from space DENIS"`

---

## manage-modelling-objects

Manages all 16 SAP Datasphere modelling object types: local tables, views, data flows, replication flows, transformation flows, task chains, analytic models, business entities, fact models, consumption models, data access controls, ER models, intelligent lookups, remote tables, currency conversion views, and time dimension views.

**Example prompts:**
- `"List all local tables in space DENIS"`
- `"Read the definition of local table PRODUCTS in space DENIS"`
- `"Create a local table ORDERS in space DENIS with columns ORDER_ID INTEGER, CUSTOMER NVARCHAR(100), TOTAL DECIMAL(15,2)"`
- `"Update local table ORDERS in space DENIS from file tmp/orders.json"`
- `"Delete view SALES_SUMMARY from space DENIS"`
- `"Deploy local table ORDERS in space DENIS"`
- `"Share analytic model REVENUE_MODEL from space DENIS to space FINANCE"`
- `"List all views in space FINANCE"`
- `"Read the analytic model KPI_MODEL in space FINANCE"`
- `"List replication flows in space DENIS"`

---

## manage-task-chains

Runs and monitors task chains, replication flows, and scheduled tasks.

**Example prompts:**
- `"Run task chain DAILY_LOAD in space DENIS"`
- `"Run replication flow RF_S4_ORDERS in space DENIS"`
- `"Stop replication flow RF_S4_ORDERS in space DENIS"`
- `"Get the status of replication flow RF_S4_ORDERS in space DENIS"`
- `"List task logs for space DENIS"`
- `"Get details of task log 12345"`
- `"Give consent for scheduled tasks in space DENIS"`
- `"Revoke consent for scheduled tasks in space DENIS"`

---

## manage-data-marketplace

Manages data providers, data products, licenses, and product releases in the Datasphere Data Marketplace.

**Example prompts:**
- `"List all data providers"`
- `"Read data provider PROVIDER_ABC"`
- `"Create a data provider from file tmp/provider.json"`
- `"List data products"`
- `"List data products from provider PROVIDER_ABC"`
- `"List licenses for provider PROVIDER_ABC"`
- `"Generate activation keys for provider PROVIDER_ABC"`
- `"List releases for data product PRODUCT_XYZ"`

---

## manage-users

Manages platform users.

> For role operations, see `manage-global-roles` and `manage-scoped-roles`.

**Example prompts:**
- `"List all users"`
- `"Create users from users.json"`
- `"Update user john.doe@company.com"`
- `"Delete user old.employee@company.com"`

---

## manage-global-roles

Manages global role definitions (CRUD operations).

**Example prompts:**
- `"List all global roles"`
- `"Read the definition of global role DW_Modeler"`
- `"Create a global role from file tmp/role.json"`
- `"Update global role DW_Modeler from file tmp/role.json"`
- `"Delete global role OLD_ROLE"`

---

## manage-scoped-roles

Manages scoped role definitions and their user/space assignments.

**Example prompts:**
- `"List all scoped roles"`
- `"Read scoped role SPACE_ADMIN"`
- `"Create a scoped role from file tmp/scoped-role.json"`
- `"List users assigned to scoped role SPACE_ADMIN"`
- `"Add user john.doe@company.com to scoped role SPACE_ADMIN"`
- `"Remove user john.doe@company.com from scoped role SPACE_ADMIN"`
- `"List spaces assigned to scoped role SPACE_ADMIN"`
- `"Add space FINANCE to scoped role SPACE_ADMIN"`
- `"Delete scoped role OLD_SCOPED_ROLE"`

---

## Repository Layout

For the full folder tree, conventions, and how the agent works end-to-end, see [AGENTS.md](../AGENTS.md).

---

## Adding a New Skill

If the agent says a skill doesn't exist for your request:

1. Create `.github/skills/<domain>/SKILL.md` — copy the structure of an existing skill folder (e.g., `manage-spaces/SKILL.md`).
2. Add YAML frontmatter (`name`, `description`), intents, CLI command templates, and a parameter table.
3. The agent will discover and use it automatically on the next request.
