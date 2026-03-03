# /new-station — Scaffold a new Titan X station app

Scaffold a new station-specific React + Azure Functions app following the exact
same patterns as the press station (Step 1). The argument is the station name.

Usage: `/new-station assembly` or `/new-station oven` or `/new-station qc`

## What to build

Each station is an independent frontend app + API sharing the same Azure SQL database.
Follow these patterns from the press station:
- Inline styles everywhere (factory terminal compatibility)
- Azure Functions v4 Node.js model (no function.json, use `app` object)
- `mssql` package with parameterized queries
- SWA managed functions via `api_location` in the workflow
- Relative `/api/` paths (SWA routes automatically)

## Steps

1. Read `CLAUDE.md` and `TODO.md` for current project context
2. Ask the user: what data does this station record? What are the key fields?
3. Create the folder structure mirroring `frontend/` and `api/`:
   ```
   stations/<name>/
   ├── frontend/
   │   ├── src/
   │   │   ├── App.tsx
   │   │   ├── components/<StationName>Form.tsx
   │   │   └── api/<stationName>.ts
   │   ├── staticwebapp.config.json  (copy from press station)
   │   ├── vite.config.ts            (copy from press station)
   │   └── package.json
   └── api/
       ├── src/
       │   ├── db.ts                 (copy from press station - identical)
       │   └── functions/<stationName>.ts
       ├── host.json                 (copy from press station - identical)
       ├── tsconfig.json             (copy from press station - identical)
       └── package.json
   ```
4. Write the DB migration in `database/migrations/00N_<name>.sql`
5. Write the GitHub Actions workflow in `.github/workflows/deploy-<name>-dev.yml`
   - Same structure as `deploy-dev.yml`
   - New SWA name: `swa-press-test-<name>-dev`
   - New secret: `AZURE_STATIC_WEB_APPS_API_TOKEN_<NAME>_DEV`
6. Add the new station to `TODO.md` under "Next Development Work"
7. Add the Azure setup steps to `TODO.md` (same pattern as the press station dev setup)

## Production Process Context

| Step | Station | Key ID | Links to |
|---|---|---|---|
| 1 | Press | `P{YYYY}{NNN}` e.g. P2026001 | — |
| 2 | Assembly | Charge ID | One or more Press Run IDs |
| 3 | Oven | Charge ID = `{year}{seq}` | Assembly charge |
| 4 | QC | Error report | Charge ID |

## Azure setup for new station (after scaffolding)

Run this pattern (swap names as needed):
```powershell
$az = 'C:\Program Files\Microsoft SDKs\Azure\CLI2\wbin\az.cmd'
& $az staticwebapp create --name swa-press-test-<name>-dev --resource-group rg-press-test-dev --location westeurope --sku Free
& $az staticwebapp appsettings set --name swa-press-test-<name>-dev --resource-group rg-press-test-dev --setting-names "SQL_CONNECTION_STRING=<same connection string as press station dev>"
& $az staticwebapp secrets list --name swa-press-test-<name>-dev --resource-group rg-press-test-dev --query "properties.apiKey" -o tsv
```
Then add the token as a GitHub secret and push.

## Recommended MCPs for this project

| MCP | Why | Install |
|---|---|---|
| **GitHub** (official) | Create PR after scaffolding, monitor workflow | `claude mcp add github` |
| **mssql** (community) | Query Azure SQL to verify migration ran correctly | `claude mcp add mssql` |
| **Filesystem** (built-in) | Already active — used for reading/writing files | Built into Claude Code |
