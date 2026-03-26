# SAP Datasphere Copilot

A GitHub Copilot custom agent that translates natural language into [SAP Datasphere CLI](https://www.npmjs.com/package/@sap/datasphere-cli) commands and executes them safely — no CLI expertise required.

> **Ask it:** "List all spaces", "Create a local table with columns X and Y", "Run the task chain DAILY_LOAD in space FINANCE", "Delete space SANDBOX" — and it handles the rest.

**→ New here? Start with the [Getting Started guide](docs/getting-started.md) — it walks you through everything from install to first command.**

---

## Skills Coverage

The agent covers all major SAP Datasphere CLI command areas:

| Skill | What it handles |
|---|---|
| `manage-log-in` | Login, logout, host config, tokens |
| `manage-spaces` | Spaces CRUD, users, DB users, workload management |
| `manage-connectivity` | TLS certificates, connections CRUD |
| `manage-modelling-objects` | All 16 object types — local tables, views, flows, analytic models, etc. |
| `manage-task-chains` | Run/stop tasks, replication flows, logs, consent |
| `manage-data-marketplace` | Providers, products, licenses, releases |
| `manage-users` | User management (list, create, update, delete) |
| `manage-global-roles` | Global roles CRUD |
| `manage-scoped-roles` | Scoped roles CRUD, user and space assignment |

See [docs/skills-reference.md](docs/skills-reference.md) for example prompts per skill.

---

## Safety Rules

- **List/read** commands execute immediately.
- **Create/update** commands execute immediately (command shown in output).
- **Delete/remove** commands require explicit `yes` or `confirm` before running.
- `DSP_CLIENT_SECRET` is never printed in chat output.

---

## Workspace Layout

| Path | Purpose |
|---|---|
| `.github/agents/datasphere-copilot.agent.md` | Custom agent definition |
| `.github/copilot-instructions.md` | Workspace-wide Copilot rules (safety, credentials, skill authority) |
| `skills/*.SKILL.md` | Domain-specific CLI command playbooks (loaded automatically by the agent) |
| `docs/` | Reference documentation |
| `.env` | Your tenant credentials — **never commit this file** |
| `.env.example` | Credential template to share with teammates |
| `tmp/` | Agent-generated temp files (gitignored) |

---

## Extending the Agent

1. Add a new `skills/<domain>.SKILL.md` file — mirror the structure of an existing skill.
2. The agent discovers skills automatically by listing the `skills/` directory.
3. Update `.env.example` if new environment variables are needed.

---

## Disclaimer

This is an independent community project and is **not affiliated with, endorsed by, or sponsored by SAP SE** or any SAP affiliate company.

SAP and SAP Datasphere are trademarks or registered trademarks of SAP SE in Germany and other countries.

This project does not bundle or redistribute the SAP Datasphere CLI or any SAP proprietary code. Users must install [`@sap/datasphere-cli`](https://www.npmjs.com/package/@sap/datasphere-cli) separately and comply with SAP's applicable license terms.

---

## License

MIT — see [LICENSE](LICENSE).
