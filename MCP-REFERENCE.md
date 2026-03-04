# MCP Server Reference

Model Context Protocol (MCP) servers extend what Claude Code can do — letting it
query databases, call APIs, search the web, and more, directly inside a conversation.

## How installation works

```bash
# Add an MCP server (run this in the project directory for project scope)
claude mcp add <name> -- <command> [args]

# With environment variables (API keys, tokens)
claude mcp add <name> -e KEY=value -- <command> [args]

# Check what's installed
claude mcp list
```

**Scope:** Running `claude mcp add` inside a project adds it to that project only
(stored in `.mcp.json` at the project root). To install globally, run the command
from outside any project directory, or copy the resulting config to `~/.claude/`.

**How it's used:** Once installed, Claude can call MCP tools automatically during
a conversation — no need to invoke them manually.

---

## Tier 1 — Install globally on every machine

These are useful in almost every project. Install once, benefit everywhere.

### GitHub (official)
Lets Claude list PRs, read issues, monitor workflow runs, create branches, and more.
The single most useful MCP for day-to-day development.

```bash
claude mcp add github -e GITHUB_TOKEN=<your-pat> -- npx -y @modelcontextprotocol/server-github
```

**Token setup:**
1. GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Scopes needed: `repo`, `read:org`, `workflow`
3. Or use a fine-grained token scoped to your repos

**What it unlocks:**
- View PR diffs and comments without leaving Claude Code
- Monitor GitHub Actions runs (supplement to `gh run watch`)
- Create issues from error logs
- List and switch branches

---

### Fetch
Lets Claude read any web page or URL — documentation, error pages, API references.
Useful when you paste a URL and want Claude to read it rather than you copying the content.

```bash
claude mcp add fetch -- npx -y @modelcontextprotocol/server-fetch
```

No credentials needed.

**What it unlocks:**
- Read library documentation from a URL
- Fetch Azure Portal REST API responses
- Read GitHub raw file URLs
- Look up error messages from official docs

---

## Tier 2 — Install per project when relevant

### PostgreSQL
For projects using PostgreSQL databases. Lets Claude query data, check table structure,
and verify migrations ran correctly — without leaving the conversation.

```bash
claude mcp add postgres -- npx -y @modelcontextprotocol/server-postgres "postgresql://user:password@host:5432/dbname"
```

---

### SQL Server / Azure SQL (community)
For projects using Azure SQL or SQL Server (like Titan X).
No official Anthropic server exists — use the community `mssql` option.

```bash
claude mcp add mssql -- npx -y @executeautomation/database-server-mcp
```

Then configure it by adding a connection config when prompted, or via the MCP settings.

**Alternative approach:** The `mssql` package can be wired up via a local proxy script.
Ask Claude to set it up when you need it — it's project-specific anyway.

**What it unlocks (for Titan X specifically):**
- `SELECT * FROM press_runs ORDER BY created_at DESC` — check live production data
- Verify a migration ran: `SELECT * FROM INFORMATION_SCHEMA.TABLES`
- Debug a specific press run ID without opening Azure Portal

**Titan X connection strings:**
```
# Dev (safe to query freely)
Server=sql-press-test-dev.database.windows.net;Database=sqldb-press-test-dev;User Id=presstestadmin;Encrypt=True

# Prod (use carefully — live data)
Server=sql-press-test-prod.database.windows.net;Database=sqldb-press-test-prod;User Id=presstestadmin;Encrypt=True
```

---

### Brave Search (web search)
Lets Claude search the web for up-to-date information — useful for debugging errors
that aren't in its training data, or for looking up recent package versions.

```bash
claude mcp add brave-search -e BRAVE_API_KEY=<key> -- npx -y @modelcontextprotocol/server-brave-search
```

**API key:** https://api.search.brave.com/ — free tier available (2000 queries/month).

---

### Filesystem (explicit paths)
Claude Code already has filesystem access to the working directory. Add this MCP only
if you need Claude to access directories *outside* the project (e.g. a shared config
folder or a different repo on the same machine).

```bash
claude mcp add filesystem -- npx -y @modelcontextprotocol/server-filesystem /path/to/allow /another/path
```

---

## Tier 3 — Situational / specialty

| MCP | When to add | Install |
|---|---|---|
| **SQLite** | Local dev with SQLite DB | `claude mcp add sqlite -- npx -y @modelcontextprotocol/server-sqlite /path/to/db.sqlite` |
| **Puppeteer** | Visual/UI testing, scraping, screenshots | `claude mcp add puppeteer -- npx -y @modelcontextprotocol/server-puppeteer` |
| **Slack** | Posting summaries or alerts to a Slack workspace | `claude mcp add slack -e SLACK_BOT_TOKEN=xoxb-...` |
| **Memory** | Persistent cross-session memory (beyond Claude Code's auto-memory) | `claude mcp add memory -- npx -y @modelcontextprotocol/server-memory` |

---

## This project: Titan X

What to install when opening the Titan X project:

```bash
# From inside the titanx project directory:

# 1. GitHub — to monitor deployments, check workflow runs
claude mcp add github -e GITHUB_TOKEN=<your-pat> -- npx -y @modelcontextprotocol/server-github

# 2. Fetch — to read Azure docs or error pages
claude mcp add fetch -- npx -y @modelcontextprotocol/server-fetch

# 3. SQL Server — to query Azure SQL without opening the Portal
claude mcp add mssql -- npx -y @executeautomation/database-server-mcp
```

The `gh` CLI is also installed on this machine and handles many GitHub operations
natively (workflow watching, PR creation), so the GitHub MCP is supplementary here.

---

## Quick reference card

| Name | Package | Credentials needed | Scope |
|---|---|---|---|
| `github` | `@modelcontextprotocol/server-github` | `GITHUB_TOKEN` (PAT) | Global |
| `fetch` | `@modelcontextprotocol/server-fetch` | None | Global |
| `postgres` | `@modelcontextprotocol/server-postgres` | Connection string | Per project |
| `mssql` | `@executeautomation/database-server-mcp` | Connection string | Per project |
| `brave-search` | `@modelcontextprotocol/server-brave-search` | `BRAVE_API_KEY` | Global |
| `filesystem` | `@modelcontextprotocol/server-filesystem` | None | Per need |
| `sqlite` | `@modelcontextprotocol/server-sqlite` | DB file path | Per project |
| `puppeteer` | `@modelcontextprotocol/server-puppeteer` | None | Per project |

---

## Current installation status (this machine)

| MCP | Installed | Notes |
|---|---|---|
| `github` | No | Add your PAT and run the Tier 1 command above |
| `fetch` | No | No credentials needed, install globally |
| `mssql` | No | Add when working on Titan X |
| `brave-search` | No | Optional, get API key at api.search.brave.com |

*Last updated: 2026-03-04*
