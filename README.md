# Claude Code Skills Collection

Personal library of reusable Claude Code slash commands (`/skill-name`).

## How to use a skill

**Project-level** (only available in one project):
Copy the `.md` file into `.claude/commands/` inside that project.

**Global** (available in every project):
Copy the `.md` file into `~/.claude/commands/` on your machine.

## Skills in this collection

| Skill | File | Scope | Description |
|---|---|---|---|
| `/deploy` | `titanx/deploy.md` | Titan X project | Push current branch, watch CI/CD, report live URL |
| `/new-station` | `titanx/new-station.md` | Titan X project | Scaffold a new factory station app following Titan X patterns |

## Recommended global MCPs

These MCP servers are worth installing globally — they add Claude Code capabilities
that are useful across most projects.

### Install globally

```bash
# GitHub — PR management, issue creation, workflow monitoring
claude mcp add github -- npx -y @modelcontextprotocol/server-github

# Filesystem — read/write local files (usually already built-in)
# Built into Claude Code by default
```

### Per-project MCPs

| MCP | When to add | Command |
|---|---|---|
| **mssql** | Any project with Azure SQL / SQL Server | `claude mcp add mssql` + connection string |
| **Postgres** | Any project with PostgreSQL | `claude mcp add postgres -- npx @modelcontextprotocol/server-postgres <conn>` |
| **Fetch** | When you need Claude to read web pages or docs | `claude mcp add fetch -- npx @modelcontextprotocol/server-fetch` |

## How to add a new skill

1. Create a `.md` file — filename = command name (e.g. `deploy.md` → `/deploy`)
2. Write instructions in markdown — Claude will follow them when you invoke the command
3. Include a "Recommended MCPs" section so future-you knows what to set up
4. Commit and push to keep the collection in sync

## Structure

```
claude-skills/
├── README.md
├── titanx/             # Titan X production tracking project
│   ├── deploy.md       # /deploy
│   └── new-station.md  # /new-station
└── global/             # Skills useful across any project (future)
```
