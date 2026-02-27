---
name: obsidian-cli
description: >
  Interact with Obsidian vaults using the official Obsidian CLI (v1.12+).
  Read, create, append, search, and manage notes, daily notes, properties, tags,
  tasks, bookmarks, templates, themes, sync, plugins, and links — all from the terminal.
  Use this skill whenever the user mentions Obsidian CLI, wants to automate Obsidian
  vault operations, manage notes programmatically, script daily note workflows,
  query vault metadata, or integrate Obsidian with AI agents or automation pipelines.
  Also trigger when the user asks about controlling Obsidian from the command line,
  building scripts around their knowledge base, or running headless Obsidian setups.
  Even if the user just says "add something to my daily note" or "search my vault"
  in a CLI/scripting context, this skill applies.
---

# Obsidian CLI

The official Obsidian CLI (released in v1.12, February 2026) lets you control every aspect of Obsidian from the terminal. It communicates with a running Obsidian desktop instance via IPC.

> For the full command reference with all subcommands, flags and examples, read
> `references/command-reference.md` in this skill folder.

## Prerequisites

| Requirement | Details |
|---|---|
| Obsidian Desktop | **v1.12.0+** |
| CLI enabled | Settings → Command line interface → Toggle ON |
| Obsidian running | The desktop app **must be running** for CLI to work (IPC) |

### Platform Notes

- **macOS / Linux**: The `obsidian` binary is registered in PATH automatically when you enable CLI in settings.
- **Windows**: Requires an `Obsidian.com` redirector file placed alongside `Obsidian.exe`. **Must run with normal user privileges** — admin terminals produce silent failures.
- **Headless Linux**: Use the `.deb` package (not snap). Run under `xvfb`. Prefix commands with `DISPLAY=:5` (or your xvfb display number). Ensure `PrivateTmp=false` if running as a service.

## Syntax

All parameters use **`key=value`** syntax. Quote values containing spaces.

```bash
obsidian <command> [subcommand] [key=value ...] [flags]
```

### Multi-Vault

Target a specific vault by making it the **first argument**:

```bash
obsidian "My Vault" daily:read
obsidian "Work Notes" search query="meeting"
```

If omitted, the CLI targets the most recently active vault.

## Command Overview

The CLI provides **100+ commands** across these groups:

| Group | Key Commands | Purpose |
|---|---|---|
| **files** | `read`, `create`, `append`, `prepend`, `move`, `delete`, `files`, `folders`, `file` | Note CRUD and file discovery |
| **daily** | `daily`, `daily:read`, `daily:append`, `daily:prepend` | Daily note operations |
| **search** | `search` | Full-text vault search with scoping and JSON output |
| **properties** | `properties`, `property:read`, `property:set`, `property:remove`, `aliases` | Frontmatter/metadata management |
| **tags** | `tags`, `tag` | Tag listing, counts, and filtering |
| **tasks** | `tasks`, `task` | Task querying, filtering, and toggling |
| **links** | `backlinks`, `links`, `unresolved`, `orphans`, `deadends` | Graph and link analysis |
| **bookmarks** | `bookmarks` | Bookmark management |
| **templates** | `templates`, `template:read`, `template:insert` | Template listing, rendering, insertion |
| **plugins** | `plugins`, `plugin:enable`, `plugin:disable`, `plugin:install`, `plugin:reload` | Plugin management |
| **sync** | `sync`, `sync:status`, `sync:history`, `sync:read`, `sync:restore`, `sync:deleted` | Obsidian Sync operations |
| **themes** | `themes` | Theme management |
| **dev** | `dev:screenshot`, `eval`, `dev:console`, `dev:errors` | Developer/debugging tools |
| **vault** | `vault`, `vaults`, `version` | Vault info and metadata |
| **other** | `outline`, `wordcount`, `recents`, `reload` | Utility commands |

## Quick Reference — Most Common Commands

### Reading & Writing Notes

```bash
obsidian read path="folder/note.md"
obsidian create path="folder/note" content="# New Note"
obsidian create path="folder/note" template="meeting-notes"
obsidian append path="folder/note.md" content="New paragraph"
obsidian prepend path="folder/note.md" content="Top content"
obsidian move path="old/note.md" to="new/note.md"
obsidian delete path="folder/note.md"
obsidian delete path="folder/note.md" permanent
```

### Daily Notes

```bash
obsidian daily                          # Open today's daily note
obsidian daily:read                     # Print content of today's note
obsidian daily:append content="- [ ] New task"
obsidian daily:prepend content="## Morning Notes"
```

### Search

```bash
obsidian search query="project alpha"
obsidian search query="TODO" path="projects" limit=10
obsidian search query="meeting" format=json
obsidian search query="urgent" matches case
```

### Properties & Tags

```bash
obsidian properties path="note.md"
obsidian property:set path="note.md" name="status" value="active"
obsidian property:read path="note.md" name="status"
obsidian property:remove path="note.md" name="draft"
obsidian tags counts sort=count
obsidian tag name="project/alpha"
```

### Tasks

```bash
obsidian tasks                          # All incomplete tasks
obsidian tasks all                      # All tasks (done + todo)
obsidian tasks done                     # Completed only
obsidian tasks daily                    # Tasks in today's daily note
obsidian task path="note.md" line=12 toggle
```

### Developer & Automation

```bash
obsidian eval code="app.vault.getFiles().length"
obsidian dev:screenshot path="screenshot.png"
obsidian dev:console limit=20
obsidian dev:errors
```

## TUI Mode

Running `obsidian` with no arguments launches an interactive TUI (Terminal User Interface):

| Key | Action |
|---|---|
| `↑↓` | Navigate files |
| `Enter` | Open file |
| `/` | Search |
| `n` | Create new file |
| `d` | Delete file |
| `r` | Rename file |
| `q` | Quit |

## Common Agent Patterns

### Daily Journal Automation

```bash
# Append a timestamped entry
obsidian daily:append content="## $(date '+%H:%M') — Status Update
- Completed: feature branch merge
- Next: code review for PR #42
- Blocked: waiting on API credentials"
```

### Create Note from Template with Metadata

```bash
obsidian create path="projects/new-feature" template="project-template"
obsidian property:set path="projects/new-feature.md" name="status" value="planning"
obsidian property:set path="projects/new-feature.md" name="created" value="$(date -I)"
obsidian daily:append content="- Started [[projects/new-feature|New Feature]]"
```

### Vault Analytics Script

```bash
obsidian files total                    # Total file count
obsidian tags counts sort=count         # Most used tags
obsidian tasks                          # Open tasks across vault
obsidian orphans                        # Notes needing integration
obsidian unresolved                     # Broken links to fix
```

### Search and Extract for AI Processing

```bash
obsidian search query="meeting notes" format=json | jq '.[] | .path'
obsidian read path="meetings/standup.md" | grep "Action item"
```

### Sync Management

```bash
obsidian sync:status                    # Check sync health
obsidian sync:history path="important.md"  # Version history
obsidian sync:restore path="important.md" version=3  # Rollback
```

## Tips

1. **Paths are vault-relative** — use `folder/note.md`, not absolute filesystem paths.
2. **`create` paths omit `.md`** — the extension is added automatically.
3. **`move` requires full target path** including `.md` extension.
4. **Pipe-friendly** — plain text output works with `grep`, `awk`, `sed`, `jq`.
5. **JSON output** — use `format=json` on search and file commands for machine parsing.
6. **Stderr noise** — GPU/Electron warnings on headless are harmless; filter with `2>/dev/null`.
7. **`daily:prepend`** inserts content after frontmatter, not at byte 0.
8. **Use `eval`** to run arbitrary JavaScript against the Obsidian API (`app.*`).

## Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| Empty output / hangs | Obsidian not running, or admin terminal (Windows) | Start Obsidian; use normal-privilege terminal |
| Command not found | CLI not registered in PATH | Re-enable CLI in Settings; restart terminal |
| Unicode errors | Fixed in v1.12.2+ | Update Obsidian |
| Wrong vault targeted | Multi-vault ambiguity | Pass vault name as first arg |
| IPC socket not found (Linux) | `PrivateTmp=true` in systemd | Set `PrivateTmp=false` |
| Snap confinement issues | Snap restricts IPC | Use `.deb` package instead |
