# Obsidian CLI — Claude Code Skill

A skill for AI coding agents that enables full control of [Obsidian](https://obsidian.md) vaults from the terminal using the official **Obsidian CLI** (v1.12+).

![Obsidian](https://img.shields.io/badge/Obsidian-v1.12%2B-7C3AED?logo=obsidian&logoColor=white)
![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-D97757)

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

| Requirement | Details |
|---|---|
| **Obsidian Desktop** | v1.12.0+ (Insider / Early Access build) |
| **CLI enabled** | Settings → Command line interface → Toggle ON |
| **Obsidian running** | The desktop app must be running — the CLI communicates over IPC |
| **Catalyst License** | $25 one-time (Early Access); planned to be free for all users later |

### Platform notes

- **macOS / Linux** — the `obsidian` binary is added to PATH automatically when you enable CLI in settings.
- **Windows** — requires an `Obsidian.com` redirector file placed alongside `Obsidian.exe` (available in the `#insider-desktop-release` channel on Obsidian Discord). Must run from a **normal-privilege terminal** — admin terminals produce silent failures.
- **Headless Linux** — use the `.deb` package (not snap). Run under `xvfb` and prefix commands with `DISPLAY=:5`. Set `PrivateTmp=false` if running as a systemd service.

---

## Installation

### Claude Code — Skills Plugin (native)

1. **Clone or download** this repository:
   ```bash
   git clone https://github.com/pablo-mano/Obsidian-CLI-skill.git
   ```
2. **Open Claude Code** and navigate to the **Skills plugin** settings.
3. **Import skill** — point to the cloned folder (or a `.zip` of it).
4. Done. The skill auto-triggers whenever you ask Claude to interact with Obsidian, automate vault operations, manage daily notes, search your vault, etc.

> The skill uses Obsidian CLI syntax and patterns from `SKILL.md` and the full command reference in `references/command-reference.md`.

---

### Cursor

Cursor loads context via [Rules](https://docs.cursor.com/context/rules-for-ai). Add the skill as a project-level or global rule:

1. Create the file `.cursor/rules/obsidian-cli.mdc` in your project root (or in `~/.cursor/rules/` for global scope).
2. Paste the body of `SKILL.md` (everything after the `---` frontmatter block) into the file.
3. Optionally append the contents of `references/command-reference.md` for the full command reference.

Cursor will now apply Obsidian CLI knowledge when you work on related tasks.

---

### Windsurf

Windsurf uses [Rules](https://docs.windsurf.com/windsurf/memories-and-rules) stored in `.windsurf/rules/`:

1. Create `.windsurf/rules/obsidian-cli.md` in your project.
2. Paste the body of `SKILL.md` (below the frontmatter) into the file.
3. Optionally include the command reference from `references/command-reference.md`.

---

### GitHub Copilot (VS Code)

Copilot supports [custom instructions](https://docs.github.com/en/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot) via a repository-level file:

1. Create `.github/copilot-instructions.md` in your repo.
2. Paste the body of `SKILL.md` into the file.

For workspace-level instructions in VS Code, add the content to your VS Code settings under `"github.copilot.chat.codeGeneration.instructions"`.

---

### Any Other LLM Agent or Assistant

`SKILL.md` is a plain Markdown document. For any agent that supports custom system prompts or instructions:

1. Open the agent's system prompt / custom instructions settings.
2. Paste the contents of `SKILL.md` (with or without the YAML frontmatter — most agents accept either).
3. Optionally include `references/command-reference.md` for the complete command reference.

This works with Aider, Continue.dev, LM Studio, Open Interpreter, or any chat interface with a system prompt field.

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

See [`references/command-reference.md`](references/command-reference.md) for the complete reference covering all commands, parameters, flags, output formatting, multi-vault usage, and headless Linux setup.

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

This skill is provided as-is for use with the Obsidian CLI. Obsidian itself requires a [Catalyst license](https://obsidian.md/pricing) for Early Access builds.
