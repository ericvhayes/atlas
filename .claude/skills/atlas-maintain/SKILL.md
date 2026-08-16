---
name: atlas-maintain
description: >
  Edit, create, or delete any file in the Atlas .ai/ configuration directory.
  Triggers on "update my soul", "add a hook", "change instructions", "add override",
  "remove hook", "edit atlas", "configure atlas", "add skill", "change my AI config".
  Always runs axis sync after changes.
allowed-tools: Bash, Read, Write
---

# Atlas Maintain

Edit the Atlas AI configuration. Atlas uses `.ai/` as the single source of truth,
projected into tool-native formats by `axis sync`.

## Atlas Directory Structure

```
.ai/
├── soul.md                   # AI identity, values, voice, behavioral guardrails
├── instructions.md           # Coding standards, architecture, testing rules
├── permissions.yaml          # Tool allow/deny policies (projected to settings)
├── ignore                    # Gitignore-style patterns — files AI must not read
├── memory/
│   ├── schema.md             # Memory file format spec
│   ├── behaviors.md          # Session startup, memory read/write/retrieval rules
│   ├── MEMORY.md             # Curated working scratchpad (2,500 char cap)
│   ├── USER.md               # User profile and preferences (1,375 char cap)
│   ├── daily/                # Daily session logs (YYYY-MM-DD.md)
│   └── transcripts/          # Transcript captures (YYYY-MM-DD.md)
├── hooks/
│   ├── session-start.md      # What to do at session start
│   ├── transcript-capture.md # How to capture session transcripts
│   └── memory-write.md       # How to save facts to MEMORY.md
├── commands/                 # Slash commands (*.md → .claude/commands/, etc.)
├── prompts/                  # Prompt templates (*.prompt.md → .github/prompts/)
├── instructions/             # Scoped per-path instructions (frontmatter: applyTo)
├── mcps/                     # MCP server registrations (YAML per server)
│   ├── ad-shim.yaml          # Example: Active Directory MCP
│   └── m365-copilot.yaml     # Example: M365 Copilot MCP
├── agents/                   # Custom agent definitions (YAML per agent)
│   ├── principal-architect.yaml
│   └── dev-team.yaml
└── overrides/
    ├── copilot.md            # Appended only to Copilot output
    ├── opencode.md           # Appended only to Opencode output
    └── claude.md             # Appended only to Claude output
```

## What Each File Controls

| File | Purpose | Edit when... |
|---|---|---|
| `soul.md` | Identity, values, voice, guardrails | Changing AI personality or behavioral rules |
| `instructions.md` | Coding standards, architecture | Adding project rules or conventions |
| `permissions.yaml` | Allow/deny tool policies | Controlling what tools AI can use |
| `ignore` | Files AI must not read | Excluding secrets, vendor, build dirs |
| `memory/schema.md` | Memory format spec | Changing memory file structure or caps |
| `memory/behaviors.md` | Memory read/write rules | Changing how memory is loaded or saved |
| `memory/MEMORY.md` | Working memory scratchpad | Adding/removing facts, threads, decisions |
| `memory/USER.md` | User profile | Updating preferences, working style |
| `hooks/session-start.md` | Session startup behavior | Changing what loads at session start |
| `hooks/transcript-capture.md` | Transcript capture rules | Changing how transcripts are saved |
| `hooks/memory-write.md` | Memory write trigger rules | Changing memory-write behavior |
| `commands/*.md` | Slash commands | Adding /commands usable in Claude/Opencode |
| `prompts/*.prompt.md` | Prompt templates | Adding reusable prompt snippets for Copilot |
| `instructions/*.md` | Scoped per-path rules | Adding rules that apply to specific file globs |
| `mcps/*.yaml` | MCP server registrations | Adding, editing, or removing MCP servers |
| `agents/*.yaml` | Custom agent definitions | Adding, editing, or removing agents |
| `overrides/{tool}.md` | Tool-specific additions | Adding rules for only one tool |

## Rules

- **Always read before writing** — understand the file's current state
- **Preserve existing structure** — match heading levels, formatting, and style
- **Don't duplicate** — check if content already exists before adding
- **Confirm destructive changes** — ask the user before removing content

## MCP Server Management

To register an MCP server, create a YAML file in `.ai/mcps/`:

```yaml
name: server-name
description: What this MCP server does
command: node
args:
  - ~/.mcp-servers/server-name/src/mcp/mcp-server.js
env: {}
transport: stdio
targets:
  - all          # or specific: copilot-cli, vscode, claude
```

Atlas will write the correct JSON config for each target tool on sync.
You can also use `axis mcps --add <name>` interactively.

## Agent Management

To register a custom agent, create a YAML file in `.ai/agents/`:

```yaml
name: Principal Architect
description: CTO-level orchestrator for development lifecycle
model: claude-opus-4.6
user_invocable: true
tools:
  - "*"
targets:
  - all
content: |
  # Principal Architect
  You are the Principal Architect...
```

Atlas writes agent files to each target tool's native format on sync.
You can also use `axis agents --add <name>` interactively.

## Permissions Management

Create `.ai/permissions.yaml` to control tool access:

```yaml
allow:
  - Read
  - Write(.ai/*)
  - Bash(cat .ai/memory/*)
deny:
  - Bash(rm -rf *)
  - WebFetch
```

Atlas projects these to Claude's `settings.json#permissions` and Opencode's
`opencode.json#permission` on sync.

## Slash Commands

Create `.ai/commands/<name>.md` for reusable slash commands:

```markdown
# Review this file for security issues.
Focus on: injection, auth bypass, data exposure.
```

Atlas projects commands to `.claude/commands/`, `.opencode/commands/`.

## Scoped Instructions

Create `.ai/instructions/<name>.md` with frontmatter for per-path rules:

```markdown
---
applyTo: "src/**/*.py"
---
# Python Rules
Use type hints on all function signatures.
```

Atlas projects to `.github/instructions/<name>.instructions.md` for Copilot.

## After Every Change

Run `axis sync` and report what files were updated.
