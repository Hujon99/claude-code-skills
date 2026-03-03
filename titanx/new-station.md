# /new-station — Titan X: Scaffold a new factory station app

## What this is
A slash command for the **Titan X Production Tracking** project.
Scaffolds a new station-specific React + Azure Functions app, following
the exact same architecture and patterns as the existing press station (Step 1).

## Which project
- **Repo:** https://github.com/Hujon99/titanx-production-tracking
- **Local path:** `C:\Users\HugoJönsson\OneDrive - Drake Analytics AB\Dokument\Testprojekt\test-online-form-2-azure`

### Project background
Titan X manufactures oil coolers for trucks. Raw material arrives as metal coils.
Each coil goes through 4 factory stations. Each station is a separate React app on
a dedicated terminal, connected to the same Azure SQL database. The goal is end-to-end
traceability: which coil ended up in which finished product, so defect batches can be
traced back to a specific supplier's coil.

The production flow:
| Step | Station | Key ID | What it records |
|---|---|---|---|
| 1 | **Press** (built) | `P{YYYY}{NNN}` | Coil barcode scanned, machine selected |
| 2 | **Assembly** (next) | Charge ID | Links a batch of press runs into a charge |
| 3 | **Oven** | `{year}{seq}` | Charge goes through oven — logs temp, time |
| 4 | **Quality Control** | Error report | Pass/fail linked to Charge ID |

## How to install this skill
```
.claude/commands/new-station.md  →  /new-station  (in the Titan X project)
```

## When to use
Invoke `/new-station <name>` when starting work on the next station app.
Examples: `/new-station assembly`, `/new-station oven`, `/new-station qc`

This is the right skill when:
- You're beginning Step 2, 3, or 4 of the production tracking system
- You need a new React frontend + Azure Functions API following the Titan X patterns
- You want the DB migration, GitHub Actions workflow, and Azure setup all scaffolded consistently

## Prerequisites before using
- Be inside the Titan X project directory
- Have read `CLAUDE.md` and `TODO.md` to understand current project state
- Know which station you're building and what data it records
- `az` CLI path: `C:\Program Files\Microsoft SDKs\Azure\CLI2\wbin\az.cmd`
- `gh` CLI authenticated

## What the skill does (steps for Claude to follow)

1. Read `CLAUDE.md` and `TODO.md` in the project root for full context.
2. Ask the user two questions:
   - What data fields does this station record? (e.g., what does the operator input?)
   - Does it need to look up or link to existing records from previous stations?
3. Scaffold the folder structure (mirror of the existing `frontend/` and `api/` structure):
   ```
   stations/<name>/
   ├── frontend/
   │   ├── src/
   │   │   ├── App.tsx                         (form → label/confirm state machine)
   │   │   ├── components/<Name>Form.tsx        (inline styles, large touch targets)
   │   │   └── api/<name>.ts                   (fetch client for /api/<name>)
   │   ├── staticwebapp.config.json            (copy from frontend/ — identical)
   │   ├── vite.config.ts                      (copy from frontend/ — identical)
   │   └── package.json
   └── api/
       ├── src/
       │   ├── db.ts                           (copy from api/src/db.ts — identical)
       │   └── functions/<name>.ts             (Azure Functions v4, app.http() routes)
       ├── host.json                           (copy from api/host.json — identical)
       ├── tsconfig.json                       (copy from api/tsconfig.json — identical)
       └── package.json
   ```
4. Write the DB migration: `database/migrations/00N_<name>.sql`
   - New table(s) for this station
   - Foreign key or coil_number reference to `press_runs` table where appropriate
5. Write the GitHub Actions workflow: `.github/workflows/deploy-<name>-dev.yml`
   - Same structure as `deploy-dev.yml` (build API, then SWA deploy)
   - New SWA name: `swa-press-test-<name>-dev`
   - New secret name: `AZURE_STATIC_WEB_APPS_API_TOKEN_<NAME>_DEV`
6. Update `TODO.md` with:
   - The new station under "Next Development Work" (mark it in_progress)
   - Azure setup steps for the new SWA (see template below)
7. Do NOT create the Azure resources automatically — add them as TODO steps for the user to confirm first.

## Coding conventions to follow (same as press station)

- **Inline styles only** — no CSS files, no Tailwind. Factory terminals may not load external CSS.
- **Large touch targets** — buttons min 48px height, inputs min 20px font size.
- **Azure Functions v4 model** — no `function.json`. Register routes with `app.http('name', { ... })`.
- **`mssql` with `input()` parameters** — no string interpolation in SQL queries.
- **ID generation inside SERIALIZABLE transaction** — SELECT COUNT + INSERT must be in the same transaction to prevent race conditions. See `api/src/functions/pressRuns.ts` for the pattern.
- **Relative `/api/` paths** in the frontend — SWA routes these automatically.
- **Error messages in English** — factory terminal operators read English.

## Azure setup template for a new station

After scaffolding, add these steps to `TODO.md`:
```powershell
$az = 'C:\Program Files\Microsoft SDKs\Azure\CLI2\wbin\az.cmd'
$NAME = "<station-name>"
$RG   = "rg-press-test-dev"
$CONN = "<copy SQL_CONNECTION_STRING from swa-press-test-dev appsettings>"

& $az staticwebapp create --name "swa-press-test-$NAME-dev" --resource-group $RG --location westeurope --sku Free
& $az staticwebapp appsettings set --name "swa-press-test-$NAME-dev" --resource-group $RG --setting-names "SQL_CONNECTION_STRING=$CONN"
& $az staticwebapp secrets list --name "swa-press-test-$NAME-dev" --resource-group $RG --query "properties.apiKey" -o tsv
# → token goes to GitHub Secret: AZURE_STATIC_WEB_APPS_API_TOKEN_<NAME>_DEV
```
Note: SWA not available in `swedencentral` — always use `westeurope` for SWA resources.
Note: SQL Server stays in `swedencentral` if creating new ones for this station.

## Recommended MCPs for this project

| MCP | Why useful | How to add |
|---|---|---|
| **GitHub** (official) | Create a PR after scaffolding, monitor the first workflow run | `claude mcp add github -- npx -y @modelcontextprotocol/server-github` |
| **mssql** (community) | Run the migration and verify the new table directly from Claude, without opening Azure Portal | `claude mcp add mssql` — connection string needed |
| **Filesystem** (built-in) | Read and copy existing station files as templates | Already active in Claude Code |

### mssql MCP connection string (dev)
```
Server=sql-press-test-dev.database.windows.net;Database=sqldb-press-test-dev;User Id=presstestadmin;Encrypt=True
```
