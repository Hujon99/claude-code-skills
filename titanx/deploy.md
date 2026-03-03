# /deploy — Titan X: Push and monitor deployment

## What this is
A slash command for the **Titan X Production Tracking** project.
Pushes the current git branch and monitors the GitHub Actions CI/CD pipeline
through to completion, then reports the live URL.

## Which project
- **Repo:** https://github.com/Hujon99/titanx-production-tracking
- **Local path:** `C:\Users\HugoJönsson\OneDrive - Drake Analytics AB\Dokument\Testprojekt\test-online-form-2-azure`
- **What it does:** Factory floor traceability app for Titan X (oil cooler manufacturer). Press station operators scan incoming coil barcodes and print a label with a generated ID.

## How to install this skill
Copy this file to the project's `.claude/commands/` directory (already done),
or copy to `~/.claude/commands/` if you want it globally available.

```
.claude/commands/deploy.md  →  /deploy  (in that project)
~/.claude/commands/deploy.md  →  /deploy  (everywhere)
```

## When to use
Invoke `/deploy` when you want to:
- Push code changes and watch the pipeline run without switching to a browser
- Catch CI failures immediately and get a summary of what broke
- Confirm which live URL the deployed code is on

## Prerequisites before using
- `gh` CLI installed and authenticated (`gh auth status`)
- `git` with a clean or committed working tree
- Be inside the Titan X project directory

## What the skill does (steps for Claude to follow)

1. Run `git status` — if there are uncommitted changes, warn the user and stop. Do not push dirty working trees.
2. Run `git branch --show-current` to identify the current branch.
3. Before pushing to `main`, always confirm: "This will deploy to **production**. Continue?" — do not push to main without explicit confirmation.
4. Run `git push origin <current-branch>`.
5. Wait ~5 seconds for GitHub to register the new run, then find it:
   `gh run list --repo Hujon99/titanx-production-tracking --limit 3`
6. Watch the run live: `gh run watch <run-id> --repo Hujon99/titanx-production-tracking`
7. On success, report the live URL:
   - `dev` branch → https://delightful-sea-065351303.2.azurestaticapps.net
   - `main` branch → https://gentle-grass-03037ee03.1.azurestaticapps.net
8. On failure, fetch the logs: `gh run view <run-id> --log-failed --repo Hujon99/titanx-production-tracking` and summarise what step failed and why.

## Environment reference

| Branch | Environment | Live URL |
|---|---|---|
| `dev` | Development | https://delightful-sea-065351303.2.azurestaticapps.net |
| `main` | Production | https://gentle-grass-03037ee03.1.azurestaticapps.net |

The CI/CD workflow (`.github/workflows/deploy-dev.yml` or `deploy-prod.yml`) does:
1. `npm ci && npm run build` in `api/` (TypeScript compile)
2. `Azure/static-web-apps-deploy@v1` — deploys frontend + managed functions to SWA

The only GitHub Secret needed per environment: `AZURE_STATIC_WEB_APPS_API_TOKEN_{DEV|PROD}`

## Recommended MCPs for this project

| MCP | Why useful | How to add |
|---|---|---|
| **GitHub** (official) | Monitor workflow runs, view PR diffs, create issues from error logs | `claude mcp add github -- npx -y @modelcontextprotocol/server-github` — needs `GITHUB_TOKEN` env var |
| **mssql** (community) | Query Azure SQL directly from Claude — check DB data, verify migrations ran, debug production issues without opening Azure Portal | `claude mcp add mssql` — needs a connection string |

### mssql MCP connection strings
```
# Dev
Server=sql-press-test-dev.database.windows.net;Database=sqldb-press-test-dev;User Id=presstestadmin;Encrypt=True

# Prod (use carefully — live data)
Server=sql-press-test-prod.database.windows.net;Database=sqldb-press-test-prod;User Id=presstestadmin;Encrypt=True
```
