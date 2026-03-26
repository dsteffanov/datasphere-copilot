# Copilot Agent Instructions for Datasphere CLI

## Purpose
This workspace contains the `datasphere-copilot` agent, which translates natural language requests into validated SAP Datasphere CLI commands and executes them safely.

## Workspace Layout
| Path | Purpose |
|---|---|
| `.github/agents/datasphere-copilot.agent.md` | Custom agent definition |
| `.github/instructions/*.instructions.md` | Object-type-specific payload references (loaded on-demand) |
| `skills/*.SKILL.md` | Domain-specific CLI command playbooks |
| `docs/` | Reference documentation for each command area |
| `.env` | Active credentials (never commit) |
| `.env.example` | Credential template to share with team |

## Rules That Apply to All Agents in This Workspace

### Skills Are Authoritative
- Before generating any Datasphere CLI command, load the matching `skills/*.SKILL.md` file.
- Skill command templates override any general LLM knowledge about CLI flags.
- If no matching skill exists, proceed using built-in CLI knowledge — never block.

### Credentials Come From .env Only
- All tenant URLs, client IDs, and secrets must be read from `.env`.
- Never hardcode or guess credential values.
- If `.env` is missing or incomplete, prompt the user to fill it in from `.env.example`.

### Safety
- List/read/describe commands: execute immediately.
- Create/update commands: execute immediately.
- Delete/remove commands: require explicit "yes" or "confirm" before executing.
- Never print `DSP_CLIENT_SECRET` in chat output.
- Never ask the user to copy and run a command manually. The agent always runs commands itself in the terminal, including any follow-up or analysis commands.

## Extending the Agent
- Add new `skills/<domain>.SKILL.md` files to support new command areas.
- Mirror the structure of existing skill files: intents, CLI templates, parameters table, safety notes.
- Update `.env.example` if new environment variables are needed.

## Example Prompts
- "Log in to the dev tenant"
- "List all spaces"
- "Create a local table XDST in space DENIS with columns ID INTEGER, NAME NVARCHAR(100)"
- "Show me the command before you run it"
- "Delete space TESTSPACE"
