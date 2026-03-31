# AGENTS.md

## Project Overview

This repository contains the **datasphere-copilot** — a custom GitHub Copilot agent that translates natural language requests into validated SAP Datasphere CLI commands and executes them safely via the VS Code terminal.

The project is **not a code project** — it contains no application source code. It is a collection of Copilot customization files (agent definition, skills, instructions) that together form an AI-driven CLI orchestration layer for SAP Datasphere.

## Repository Structure

```
.
├── .github/
│   ├── agents/
│   │   └── datasphere-copilot.agent.md    # Custom agent definition
│   ├── instructions/                       # On-demand payload references (loaded by description match)
│   │   ├── local-tables.instructions.md
│   │   ├── spaces.instructions.md
│   │   ├── scoped-roles.instructions.md
│   │   └── sql-views.instructions.md
│   └── skills/                             # Domain-specific CLI command playbooks
│       ├── manage-log-in/SKILL.md
│       ├── manage-spaces/SKILL.md
│       ├── manage-modelling-objects/SKILL.md
│       ├── manage-scoped-roles/SKILL.md
│       ├── manage-global-roles/SKILL.md
│       ├── manage-users/SKILL.md
│       ├── manage-connectivity/SKILL.md
│       ├── manage-task-chains/SKILL.md
│       └── manage-data-marketplace/SKILL.md
├── docs/                    # Human-readable documentation
│   ├── getting-started.md
│   └── skills-reference.md
├── tmp/                     # Gitignored working directory for CLI payloads and outputs
├── .env                     # Active credentials (never commit)
├── .env.example             # Credential template
└── AGENTS.md                # This file
```

## How the Agent Works

1. User sends a natural-language request in Copilot Chat (e.g., "List all spaces" or "Create a local table ORDERS in space SALES")
2. The agent matches the intent to a skill in `.github/skills/`
3. It reads the skill's `SKILL.md` for CLI command templates and payload structures
4. For create/update operations, VS Code auto-injects matching instruction files with detailed payload schemas
5. It builds the CLI command, writes any needed JSON payloads to `tmp/`, and executes in the terminal
6. It responds with a plain-English summary of what happened

## Key Conventions

### Skills
- Each skill is a folder under `.github/skills/<skill-name>/` containing a `SKILL.md`
- Skills have YAML frontmatter with `name` (matching folder name) and `description`
- Skills are the authoritative source for CLI command syntax — they override general LLM knowledge

### Instructions
- Instruction files in `.github/instructions/` are loaded on-demand based on their `description` field
- They provide detailed payload schemas for specific object types (local tables, views, spaces, roles)
- No `applyTo` patterns — these are task-triggered, not file-triggered

### Credentials
- All tenant URLs, client IDs, and secrets come from `.env` (never hardcoded)
- The agent reads `.env` and generates `tmp/env.json` for the CLI login flow
- Never print `DSP_CLIENT_SECRET` in chat output

### Safety
- Read/list commands: execute immediately
- Create/update commands: execute immediately
- Delete/remove commands: require explicit user confirmation

## Extending the Agent

1. **New CLI domain**: Create a new folder `.github/skills/<domain>/SKILL.md` with frontmatter, intents, CLI templates, parameters, and safety notes
2. **New instruction**: Add `.github/instructions/<topic>.instructions.md` with a keyword-rich `description` for on-demand loading
4. **New credentials**: Update `.env.example` with new variable names and descriptions
