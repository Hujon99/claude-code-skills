# Claude Code Skills Collection

Personal library of reusable Claude Code slash commands (`/skill-name`).

## How to use a skill

**Project-level** (only available in one project):
Copy the `.md` file into `.claude/commands/` inside that project.

**Global** (available in every project):
Copy the `.md` file into `~/.claude/commands/` on your machine.
On Windows: `C:\Users\HugoJönsson\.claude\commands\`

## Skills in this collection

### Global (available in every project)
| Skill | File | Trigger | Description |
|---|---|---|---|
| `/skill-creator` | `global/skill-creator.md` | "create a skill", "make a new skill", "build a slash command" | Full Anthropic framework: draft → test → eval → iterate → publish. Use this to create any new skill. Source: [anthropics/skills](https://github.com/anthropics/skills/tree/main/skills/skill-creator) |

### Project-specific
| Skill | File | Project | Description |
|---|---|---|---|
| `/deploy` | `titanx/deploy.md` | Titan X | Push current branch, watch CI/CD, report live URL |
| `/new-station` | `titanx/new-station.md` | Titan X | Scaffold a new factory station app following Titan X patterns |

## Installation status

| Skill | Global (`~/.claude/commands/`) | Project (`.claude/commands/`) |
|---|---|---|
| `skill-creator` | ✅ installed | — |
| `deploy` (Titan X) | — | ✅ in titanx repo |
| `new-station` (Titan X) | — | ✅ in titanx repo |

## MCP servers

See **[MCP-REFERENCE.md](MCP-REFERENCE.md)** for the full guide — what to install globally,
what to install per project, credentials needed, and current status on this machine.

**Quick start — install these globally first:**
```bash
claude mcp add github -e GITHUB_TOKEN=<your-pat> -- npx -y @modelcontextprotocol/server-github
claude mcp add fetch -- npx -y @modelcontextprotocol/server-fetch
```

## How to add a new skill

The best way is to use `/skill-creator` — it walks you through the whole process:
draft → write test cases → run evals → iterate → optimise the trigger description.

Manual approach:
1. Create a `.md` file — filename = command name (e.g. `deploy.md` → `/deploy`)
2. Add YAML frontmatter with `name` and `description` (description controls when it triggers)
3. Write instructions in markdown — Claude follows them when you invoke the command
4. Include a "Recommended MCPs" section so future-you knows what to set up
5. Put global skills in `global/`, project-specific in a folder named after the project
6. Commit and push to keep the collection in sync

## Structure

```
claude-skills/
├── README.md
├── MCP-REFERENCE.md            # Full MCP install guide (all tiers, credentials, status)
├── global/                     # Skills installed globally (~/.claude/commands/)
│   └── skill-creator.md        # /skill-creator — create and test new skills
└── titanx/                     # Titan X production tracking project
    ├── deploy.md               # /deploy
    └── new-station.md          # /new-station
```
