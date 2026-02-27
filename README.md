# Obsidian CLI — Claude Code Skill

A skill for AI coding agents that enables full control of [Obsidian](https://obsidian.md) vaults from the terminal using the official **Obsidian CLI** (v1.12+).

![Obsidian](https://img.shields.io/badge/Obsidian-v1.12%2B-7C3AED?logo=obsidian&logoColor=white)
![Claude Code](https://img.shields.io/badge/Claude_Code-Plugin-D97757)
![Claude Code Plugin](https://img.shields.io/badge/Anthropic-Compatible-CC785C)

---

## What This Skill Does

Once installed, your AI agent will know how to interact with Obsidian vaults through the official CLI — reading, creating, and editing notes; managing daily notes; running full-text search; querying tasks, tags, links, and properties; managing plugins and sync; and running developer tools.

Covers **100+ commands** across all major areas:

| Area | Commands |
|---|---|
| **Files** | `read`, `create`, `append`, `prepend`, `move`, `delete`, `files`, `folders` |
| **Daily Notes** | `daily`, `daily:read`, `daily:append`, `daily:prepend` |
| **Search** | `search` with scoping, limits, JSON output |
| **Properties** | `properties`, `property:read`, `property:set`, `property:remove` |
| **Tags** | `tags`, `tag` with counts and filtering |
| **Tasks** | `tasks`, `task` — query, filter, toggle |
| **Links** | `backlinks`, `links`, `unresolved`, `orphans`, `deadends` |
| **Templates** | `templates`, `template:read`, `template:insert` |
| **Plugins** | `plugins`, `plugin:enable/disable/install/reload` |
| **Sync** | `sync`, `sync:status`, `sync:history`, `sync:restore` |
| **Developer** | `eval`, `dev:screenshot`, `dev:console`, `dev:errors` |
| **Vault** | `vault`, `vaults`, `version`, `recents`, `outline`, `wordcount` |

---

## Prerequisites

> Obsidian v1.12 is available to **all users** — no Early Access build or Catalyst license required.

| Requirement | Details |
|---|---|
| **Obsidian Desktop** | v1.12.0+ |
| **CLI enabled** | Settings → Command line interface → Toggle ON |
| **Obsidian running** | The desktop app must be running — the CLI communicates over IPC |

### Platform notes

- **macOS / Linux** — the `obsidian` binary is added to PATH automatically when you enable CLI in settings.
- **Windows** — requires an `Obsidian.com` redirector file placed alongside `Obsidian.exe`. Must run from a **normal-privilege terminal** — admin terminals produce silent failures.
- **Headless Linux** — use the `.deb` package (not snap). Run under `xvfb` and prefix commands with `DISPLAY=:5`. Set `PrivateTmp=false` if running as a systemd service.

---

## Installation

### Claude Code — Plugin (native)

1. Clone this repository:
   ```bash
   git clone https://github.com/pablo-mano/Obsidian-CLI-skill
   ```
2. Load the plugin with the `--plugin-dir` flag:
   ```bash
   claude --plugin-dir ./Obsidian-CLI-skill
   ```
3. Done — the skill auto-triggers when you ask Claude to interact with Obsidian, automate vault operations, manage daily notes, search your vault, etc.

**Persistent setup** — add to your project's `.claude/settings.json` so the plugin loads automatically:
```json
{
  "plugins": {
    "obsidian-cli": {
      "source": { "source": "github", "repo": "pablo-mano/Obsidian-CLI-skill" }
    }
  }
}
```

> The `.claude-plugin/plugin.json` manifest makes this repository directly compatible with Claude Code's plugin system.

---

### Cursor

Cursor has a native skills system (launched Feb 2026) that is directly compatible with the `SKILL.md` format — no rules file needed.

**Option A — GitHub install (recommended):**
1. Open Cursor Settings → Rules
2. Click **Add Rule** → **Remote Rule (GitHub)**
3. Paste `https://github.com/pablo-mano/Obsidian-CLI-skill`
4. Done — the skill auto-triggers on Obsidian-related tasks.

**Option B — Local copy:**
1. Clone or download this repo
2. Copy the folder into your project: `.cursor/skills/obsidian-cli/`
   (or globally: `~/.cursor/skills/obsidian-cli/`)
3. Cursor picks it up automatically — no restart needed.

---

### Windsurf

Windsurf uses a Rules system (`.windsurf/rules/`). Rules have a 12,000 character limit per file, so split the skill across two files:

1. Create `.windsurf/rules/obsidian-cli.md` — paste the body of `SKILL.md` (everything after the `---` frontmatter).
2. Create `.windsurf/rules/obsidian-cli-reference.md` — paste the contents of `skills/obsidian-cli/references/command-reference.md`.

Set the activation mode to **Always On** or **Model Decision** in each file's frontmatter to control when the rules apply.

---

### Nanoclaw

Nanoclaw loads skills from `.claude/skills/` using the same `SKILL.md` format — directly compatible.

1. Clone or download this repo
2. Copy the skill folder into your Nanoclaw project:
   ```bash
   cp -r Obsidian-CLI-skill /path/to/nanoclaw/.claude/skills/obsidian-cli
   ```
3. The skill is available as a slash command (`/obsidian-cli`) or auto-triggers when you ask the agent to interact with Obsidian.

> This is a knowledge skill — it adds Obsidian CLI awareness to the agent without modifying Nanoclaw's codebase.

---

### Openclaw

Openclaw loads skills from the `skills/` directory at the project root using the `SKILL.md` format — directly compatible.

1. Clone or download this repo
2. Copy the skill folder into Openclaw's skills directory:
   ```bash
   cp -r Obsidian-CLI-skill /path/to/openclaw/skills/obsidian-cli
   ```
3. Restart the Openclaw agent — skills are discovered automatically.

---

### GitHub Copilot (VS Code)

Copilot supports [custom instructions](https://docs.github.com/en/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot) via repository files:

**Repository-wide** (applies to all files):
1. Create `.github/copilot-instructions.md` in your repo.
2. Paste the body of `SKILL.md` into the file.

**Path-scoped** (optional alternative):
1. Create `.github/instructions/obsidian-cli.instructions.md`
2. Add frontmatter `applyTo: "**/*"` then paste the `SKILL.md` body.

---

### Any Other LLM Agent or Assistant

`SKILL.md` is a plain Markdown document. For any agent that supports custom system prompts or instructions:

1. Open the agent's system prompt / custom instructions settings.
2. Paste the contents of `SKILL.md` (with or without the YAML frontmatter — most agents accept either).
3. Optionally include `skills/obsidian-cli/references/command-reference.md` for the complete command reference.

This works with any chat interface or coding agent that has a system prompt or custom instructions field.

---

## Quick Examples

### Append to today's daily note

```bash
obsidian daily:append content="- [ ] Review pull requests"
```

### Search your vault and pipe to jq

```bash
obsidian search query="meeting notes" format=json | jq '.[].path'
```

### Vault health check

```bash
obsidian files total          # Total file count
obsidian tags counts sort=count  # Most-used tags
obsidian tasks                # All open tasks
obsidian orphans              # Notes with no links
obsidian unresolved           # Broken wikilinks
```

### Create a note from a template with metadata

```bash
obsidian create path="projects/new-feature" template="project-template"
obsidian property:set path="projects/new-feature.md" name="status" value="planning"
obsidian property:set path="projects/new-feature.md" name="created" value="$(date -I)"
obsidian daily:append content="- Started [[projects/new-feature|New Feature]]"
```

---

## Full Command Reference

See [`skills/obsidian-cli/references/command-reference.md`](skills/obsidian-cli/references/command-reference.md) for the complete reference covering all commands, parameters, flags, output formatting, multi-vault usage, and headless Linux setup.

---

## Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| Empty output / hangs | Obsidian not running, or admin terminal (Windows) | Start Obsidian; use a normal-privilege terminal |
| `command not found` | CLI not registered in PATH | Re-enable CLI in Settings; restart terminal |
| Unicode errors | Bug fixed in v1.12.2+ | Update Obsidian |
| Wrong vault targeted | Multi-vault ambiguity | Pass vault name as the first argument: `obsidian "My Vault" ...` |
| IPC socket not found (Linux) | `PrivateTmp=true` in systemd unit | Set `PrivateTmp=false` |
| Snap confinement errors | Snap restricts IPC | Use the `.deb` package instead |

---

## License

This skill is provided as-is for use with the Obsidian CLI. Obsidian v1.12+ is free for all users.
