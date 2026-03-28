# Skill: Manage Log In

## Supported Intents
- Log in to tenant
- Log in using options file
- Log in with client credentials (technical user)
- Log out
- Show current host
- Set host
- Clean/remove stored host
- Show stored secrets/tokens
- Initialize CLI cache
- Show passcode URL
- Initialize with access token
- Initialize with secrets file

## CLI Command Templates

### Login — Interactive (OAuth via browser)
```
datasphere login
  --host "<url>"
  --client-id "<id>"
  --client-secret "<secret>"
  [--authorization-url "<url>"]
  [--token-url "<url>"]
  [--authorization-flow "authorization_code"]
  [--browser "chrome|edge|firefox"]
  --force
```

### Login — Technical User (no browser, client credentials)
```
datasphere login
  --host "<url>"
  --client-id "<id>"
  --force
  --client-secret "<secret>"
  --authorization-flow "client_credentials"
```

### Login via options file (RECOMMENDED — use this always)

**Step 1 — Always ensure `tmp/env.json` exists first.**
The `tmp/` folder is gitignored and may not exist. Before every login, run this single PowerShell command to (re)create it from `.env`:

```powershell
$lines=Get-Content .env;function Get-EnvValue([string]$name){$m=$lines|Where-Object{$_ -match "^$name="}|Select-Object -First 1;if($null -eq $m){return ''};return ($m -split '=',2)[1].Trim()};$tenantHost=Get-EnvValue 'DSP_HOST';$clientId=Get-EnvValue 'DSP_CLIENT_ID';$clientSecret=Get-EnvValue 'DSP_CLIENT_SECRET';$authUrl=Get-EnvValue 'DSP_AUTHORIZATION_URL';$tokenUrl=Get-EnvValue 'DSP_TOKEN_URL';$browser=Get-EnvValue 'DSP_BROWSER';$flow=Get-EnvValue 'DSP_AUTHORIZATION_FLOW';if([string]::IsNullOrWhiteSpace($authUrl) -or [string]::IsNullOrWhiteSpace($tokenUrl)){try{$u=[uri]$tenantHost;$parts=$u.Host -split '\.';if($parts.Length -ge 2){$tenantSubdomain=$parts[0];$region=$parts[1];if([string]::IsNullOrWhiteSpace($authUrl)){$authUrl="https://$tenantSubdomain.authentication.$region.hana.ondemand.com/oauth/authorize"};if([string]::IsNullOrWhiteSpace($tokenUrl)){$tokenUrl="https://$tenantSubdomain.authentication.$region.hana.ondemand.com/oauth/token"}}}catch{}};$j=[ordered]@{'client-id'=$clientId;'client-secret'=$clientSecret;'authorization-url'=$authUrl;'token-url'=$tokenUrl;'host'=$tenantHost;'browser'=$browser;'authorization-flow'=$flow};if(!(Test-Path tmp)){New-Item -ItemType Directory tmp|Out-Null};$json=($j|ConvertTo-Json) -replace ':\s{2,}',': ';[System.IO.File]::WriteAllText((Join-Path (Get-Location) 'tmp\env.json'),$json,[System.Text.UTF8Encoding]::new($false))
```

This is safe to run even when the file already exists — it overwrites with fresh values.

**Step 2 — Login:**
```
datasphere login --options-file tmp/env.json --force
```
This is the only reliable approach when a cached token may be stale. Direct flags cause the CLI to attempt a token refresh (which fails with 401 if expired) instead of starting a fresh browser auth flow.

> **Important:** `tmp/env.json` must be BOM-free UTF-8. The command above uses `[System.Text.UTF8Encoding]::new($false)` to guarantee no BOM. Never use PowerShell's `Set-Content -Encoding UTF8` — it adds a BOM that silently breaks the CLI parser. Also, `ConvertTo-Json` outputs two spaces after `:` — the `-replace` above normalises this to one space.

Options file format (`tmp/env.json`, gitignored):
```json
{
    "client-id": "<id>",
    "client-secret": "<secret>",
    "authorization-url": "<url>",
    "token-url": "<url>",
    "host": "<url>",
    "browser": "<browser-name>",
    "authorization-flow": "<flow>"
}
```

### Logout
```
datasphere logout [--login-id <id>]
```

### Host management
```
datasphere config host set <url>       # Persist tenant URL (avoids --host on every command)
datasphere config host show            # Display currently stored host URL
datasphere config host clean           # Remove stored host URL
```

### Show stored secrets/tokens
```
datasphere config secrets show
```

### Initialize the CLI cache (required for passcode-based auth, optional for OAuth)
```
datasphere config cache init --host <url> [--passcode <passcode>]
datasphere config cache init --host <url> --access-token <token>
datasphere config cache init --host <url> --secrets-file /path/to/secrets.json
```
Secrets file format:
```json
{
  "tenantUrl": "https://mytenant.eu10.hcs.cloud.sap",
  "access_token": "<access_token>",
  "refresh_token": "<refresh_token>",
  "client_id": "<client_id>",
  "client_secret": "<client_secret>"
}
```

### Get passcode URL
```
datasphere passcode-url --host <url>
```

## Parameters
| Parameter | Required | Notes |
|-----------|----------|-------|
| --host | Yes | Tenant URL (e.g. `https://mytenant.us21.hcs.cloud.sap`) |
| --client-id | Yes (login) | OAuth Client ID |
| --client-secret | Yes (login) | OAuth Client Secret |
| --authorization-url | No | OAuth Authorization URL — provided by admin |
| --token-url | No | OAuth Token URL — provided by admin |
| --authorization-flow | No | `authorization_code` (default, interactive) or `client_credentials` (technical user) |
| --browser | No | `chrome` (default), `edge`, `firefox` |
| --options-file | No | Path to JSON file with all login options |
| --login-id | No | For logout: specify which login session to remove |
| --passcode | No | Temporary passcode for cache init |
| --access-token | No | Access token to initialize cache directly |
| --secrets-file | No | Path to secrets JSON file |

## Environment Variables (supported by CLI)
| Variable | Description |
|----------|-------------|
| `HOST` | Tenant URL — same as `--host` |
| `CLIENT_ID` | OAuth client ID |
| `CLIENT_SECRET` | OAuth client secret |
| `CLI_HTTP_PORT` | Port for OAuth callback server (default: 8080) |
| `LOG_LEVEL` | Log verbosity: 1=off, 2=error, 3=warn, 4=info, 5=debug, 6=trace |

## API Rate Limiting
- Max ~300 requests/user/minute
- HTTP 429 if exceeded; check `X-Ratelimit-Remaining` and `Retry-After` headers

## Safety
- Never display `--client-secret` value in logs or output
- Load all secrets from `.env` only — never hardcode
- OAuth Interactive Usage: requires browser callback at `http://localhost:8080`
- Technical User (client_credentials): no browser required — safe for CI/CD pipelines

## Required Role
- Interactive Usage OAuth client: all commands
- Technical User OAuth client: `marketplace`, `tasks`, `objects`, `configuration certificates`, `spaces connections` only

## Examples
- "Log in to the dev tenant"
- "Log in using client credentials"
- "Log out"
- "Show the current host"
- "Set host to https://mytenant.eu10.hcs.cloud.sap"
- "Clean stored host"
- "Show stored tokens"
- "Initialize CLI cache"
- "Get passcode URL"