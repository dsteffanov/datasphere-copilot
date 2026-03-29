# Getting Started

This guide walks you through everything you need — from a blank machine to your first Datasphere command in VS Code.

> **Where to run commands:** All terminal commands in this guide can be run in any of the following:
> - **Windows:** PowerShell (recommended) or Command Prompt (`cmd`)
> - **macOS/Linux:** Terminal
>
> To open PowerShell on Windows: press `Win + X` → **Windows PowerShell** (or search for "PowerShell" in the Start menu).

---

## 1. Install Node.js

The SAP Datasphere CLI requires Node.js 18 or later.

1. Go to [nodejs.org](https://nodejs.org/) and download the **LTS** version for your OS.
2. Run the installer and follow the prompts (default settings are fine).
3. Open a new terminal window and verify the installation:

```
node --version
```

You should see something like `v22.x.x`. If not, restart your terminal or rerun the installer.

---

## 2. Install the SAP Datasphere CLI

Open a terminal and run:

```
npm install -g @sap/datasphere-cli
```

Verify:
```powershell
datasphere --version
```

> If you see `datasphere: command not found`, close and reopen your terminal — npm global installs sometimes require a fresh session to appear on the PATH.

> **Legal note:** By installing `@sap/datasphere-cli` you agree to SAP's applicable license terms. This project does not bundle or redistribute the CLI — it only orchestrates commands on your behalf. SAP and SAP Datasphere are trademarks of SAP SE.

---

## 3. Install VS Code and the GitHub Copilot Extension

1. Install [VS Code 1.99+](https://code.visualstudio.com/) or [VS Code Insiders](https://code.visualstudio.com/insiders/) (Insiders always includes the latest Agent mode support).
2. Open VS Code and install the [GitHub Copilot extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) from the Extensions panel (`Ctrl+Shift+X`).
3. Sign in with a GitHub account that has an active Copilot license.

---

## 4. Clone the Repo

```
git clone https://github.com/dsteffanov/datasphere-copilot.git
cd datasphere-copilot
```

Open the folder in VS Code: **File → Open Folder** and select the `datasphere-copilot` folder.

> If you don't have Git installed, download it from [git-scm.com](https://git-scm.com/).

---

## 5. Configure Credentials

### 5a. Copy the environment template

**PowerShell:**
```powershell
Copy-Item .env.example .env
```

**cmd:**
```cmd
copy .env.example .env
```

**macOS/Linux:**
```bash
cp .env.example .env
```

### 5b. Fill in your values

Open `.env` in VS Code (or any text editor). The file has the following variables:

**Required — the agent cannot function without these:**

| Variable | Description |
|---|---|
| `DSP_HOST` | Your Datasphere tenant URL, e.g. `https://your-tenant.us21.hcs.cloud.sap` |
| `DSP_CLIENT_ID` | OAuth client ID |
| `DSP_CLIENT_SECRET` | OAuth client secret |

**Optional — provided by your administrator or left as defaults:**

| Variable | Description | Default |
|---|---|---|
| `DSP_AUTHORIZATION_URL` | OAuth authorization endpoint | Derived from host if not set |
| `DSP_TOKEN_URL` | OAuth token endpoint | Derived from host if not set |
| `DSP_AUTHORIZATION_FLOW` | Login flow type | `authorization_code` |
| `DSP_BROWSER` | Browser for OAuth popup: `chrome`, `edge`, or `firefox` | `edge` |
| `DSP_DEFAULT_SPACE` | Your most-used space ID — agent uses this as fallback | _(none)_ |
| `DSP_ENV_NAME` | Label for this environment, e.g. `dev`, `prod` | `dev` |

### Where to find your credentials

All OAuth credentials come directly from your SAP Datasphere tenant:

1. Log in to your SAP Datasphere tenant.
2. Go to **System → Administration → App Integration**.
3. Under **OAuth Clients**, create or select an OAuth client.
4. Copy the **Client ID** → `DSP_CLIENT_ID`
5. Copy the **Client Secret** → `DSP_CLIENT_SECRET`
6. Your tenant URL (visible in the browser address bar) → `DSP_HOST`

> **Security:** `.env` is listed in `.gitignore` and must never be committed. Share credentials with teammates via a secure secrets manager, not via this file.

---

## 6. Open the Agent in VS Code

1. Press `Ctrl+Alt+I` (Windows/Linux) or `Cmd+Alt+I` (macOS) to open Copilot Chat.
2. In the chat panel (below the prompt), directly pick **datasphere-copilot** as the agent (you can also use `Ctrl+.` as a shortcut).
3. Select your preferred model, or keep it as Auto to reduce cost.


> If the agent doesn't appear, verify that the `.github/agents/` folder is present in the workspace root.

> **Model recommendation:** In my experience, Claude Sonnet 4.6 and GPT-5.3-Codex delivered the most consistent results for this type of code generation.
> 
> When choosing your Copilot model, keep in mind that accuracy can vary, especially with complex commands and payloads. It’s also worth tracking your premium request quota, as different models and reasoning settings can significantly increase costs.

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
| Missing `DSP_AUTHORIZATION_URL` | Find it in your Datasphere tenant under **System → Administration → App Integration** |
| Node.js version error | Run `node --version` — must be 18 or later |
| `npm` not found after Node install | Close and reopen your terminal, or restart your machine |

---

## Next Steps

- See the [Skills Reference](skills-reference.md) for the full list of commands and example prompts.
- Add a new `skills/<domain>.SKILL.md` to teach the agent new commands.
