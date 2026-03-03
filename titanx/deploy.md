# /deploy — Push and monitor deployment

Push the current branch to GitHub and watch the CI/CD pipeline to completion.

## Steps

1. Check which branch is currently checked out with `git branch --show-current`
2. Run `git push origin <current-branch>`
3. Find the triggered workflow run with `gh run list --repo Hujon99/titanx-production-tracking --limit 1`
4. Watch it live with `gh run watch <run-id> --repo Hujon99/titanx-production-tracking`
5. Report success or failure. If success, show the live URL for the environment:
   - `dev` branch → https://delightful-sea-065351303.2.azurestaticapps.net
   - `main` branch → https://gentle-grass-03037ee03.1.azurestaticapps.net
6. If the workflow fails, fetch the failure logs with `gh run view <run-id> --log-failed --repo Hujon99/titanx-production-tracking` and summarise the error.

## Notes
- Do not push if there are uncommitted changes — warn the user first
- Do not push to `main` directly without asking for confirmation

## Recommended MCPs for this project
Install these in your Claude Code settings for the best experience:

| MCP | Why | Install |
|---|---|---|
| **GitHub** (official) | Monitor workflow runs, view PR checks, create issues | `claude mcp add github` |
| **mssql** (community) | Query Azure SQL directly from Claude without Portal | `claude mcp add mssql` — needs connection string |

### mssql MCP config for dev DB
When setting up, point it at:
`Server=sql-press-test-dev.database.windows.net;Database=sqldb-press-test-dev;User Id=presstestadmin;Encrypt=True`
