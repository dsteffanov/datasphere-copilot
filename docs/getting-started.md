# Getting Started

This guide walks you through everything you need — from a blank machine to your first Datasphere command in VS Code.

---

## 1. Install Node.js

The SAP Datasphere CLI requires Node.js 18 or later.

Download and install the latest LTS release from [nodejs.org](https://nodejs.org/).

Verify:

```bash
node --version
```

---

## 2. Install the SAP Datasphere CLI

```bash
npm install -g @sap/datasphere-cli
```

Verify:

```bash
datasphere --version
```

> **Legal note:** By installing `@sap/datasphere-cli` you agree to SAP's applicable license terms. This project does not bundle or redistribute the CLI — it only orchestrates commands on your behalf. SAP and SAP Datasphere are trademarks of SAP SE.

---

## 3. Install VS Code and the GitHub Copilot Extension

1. Install [VS Code 1.99+](https://code.visualstudio.com/) or [VS Code Insiders](https://code.visualstudio.com/insiders/) (Insiders always includes the latest Agent mode support).
2. Open VS Code and install the [GitHub Copilot extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) from the Extensions panel.
3. Sign in with a GitHub account that has an active Copilot license.

---

## 4. Clone the Repo

```bash
git clone https://github.com/your-org/datasphere-copilot.git
cd datasphere-copilot
```

Open the folder in VS Code: **File → Open Folder**.

---

## 5. Configure Credentials

### 5a. Copy the environment template

On Windows (PowerShell):
```powershell
Copy-Item .env.example .env
```

On macOS/Linux:
```bash
cp .env.example .env
```

### 5b. Fill in your values

Open `.env` and fill in the following:

| Variable | Description |
|---|---|
| `DSP_HOST` | Your Datasphere tenant URL, e.g. `https://your-tenant.us21.hcs.cloud.sap` |
| `DSP_CLIENT_ID` | OAuth client ID from SAP BTP cockpit |
| `DSP_CLIENT_SECRET` | OAuth client secret from SAP BTP cockpit |
| `DSP_AUTHORIZATION_URL` | OAuth authorization endpoint (from BTP service binding) |
| `DSP_TOKEN_URL` | OAuth token endpoint (from BTP service binding) |
| `DSP_AUTHORIZATION_FLOW` | Leave as `authorization_code` for browser-based login |
| `DSP_BROWSER` | Browser to use for OAuth: `chrome`, `edge`, or `firefox` |
| `DSP_DEFAULT_SPACE` | Optional: your most-used space ID (agent uses this as default) |
| `DSP_ENV_NAME` | Optional label for this environment, e.g. `dev`, `prod` |

### Where to find your credentials

1. Log in to [SAP BTP Cockpit](https://account.hana.ondemand.com/).
2. Navigate to your subaccount → **Instances and Subscriptions**.
3. Find your Datasphere service instance → **Service Bindings** or **Service Keys**.
4. The binding JSON contains `clientid`, `clientsecret`, `url` (token URL), and `uaa.url` (authorization URL).

> **Security:** `.env` is listed in `.gitignore` and must never be committed. Share credentials with teammates via a secure secrets manager, not via this file.

---

## 6. Open the Agent in VS Code

1. Open Copilot Chat with `Ctrl+Alt+I` (Windows/Linux) or `Cmd+Alt+I` (macOS).
2. Make sure you are in **Agent mode** — use the mode dropdown at the top of the chat panel (not Ask or Edit).
3. Click the **agent picker** (`@` icon) and select **`datasphere-copilot`**.

> If the agent doesn't appear, verify that the `.github/agents/` folder is present in the workspace root and that you are in Agent mode.

---

## 7. Log In and Start Working

Type your first instruction in plain English — for example:

```
Log in to the dev tenant
```

The agent reads your `.env` credentials and runs the appropriate CLI login command. Depending on your configured login flow, a browser window may open for OAuth authorization.

Once authenticated, try any command:

```
List all spaces
```

```
Create a local table ORDERS in space FINANCE with columns ORDER_ID INTEGER, CUSTOMER NVARCHAR(100)
```

You're ready to manage your Datasphere tenant with plain English.

---

## Troubleshooting

| Issue | Fix |
|---|---|
| Agent not visible in picker | Ensure VS Code is in Agent mode; verify `.github/agents/` folder exists |
| `datasphere: command not found` | Run `npm install -g @sap/datasphere-cli` and restart your terminal |
| `401 Unauthorized` on login | Say `"Log in again"` — the agent uses `--force` to refresh stale tokens |
| Browser doesn't open during login | Check `DSP_BROWSER` in `.env` — must be `chrome`, `edge`, or `firefox` |
| Missing `DSP_AUTHORIZATION_URL` | Find it in your BTP service binding under `uaa.url` + `/oauth/authorize` |
| Node.js version error | Run `node --version` — must be 18 or later |

---

## Next Steps

- See the [Skills Reference](skills-reference.md) for the full list of commands and example prompts.
- Add a new `skills/<domain>.SKILL.md` to teach the agent new commands.
