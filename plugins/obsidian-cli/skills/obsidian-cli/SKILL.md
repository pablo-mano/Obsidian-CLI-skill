---
name: obsidian-cli
description: >
  Use this skill whenever the user wants Claude to directly interact with their
  Obsidian vault — reading a note or daily note, writing or appending content,
  searching vault contents, counting or listing notes, managing tasks, moving or
  renaming files, finding orphaned notes or broken links. Without this skill, Claude
  has no way to access vault data or execute vault operations. Treat any request that
  implies "go into my vault and do X" as a trigger — the user is asking Claude to act,
  not to explain. Also trigger for vault automation, CLI scripting, or cron-based
  workflows involving Obsidian. Skip for pure conceptual questions: how Obsidian's GUI
  works, navigating settings menus, theme or plugin installation via the UI, or general
  Dataview query syntax — anything where the user needs an explanation rather than
  Claude performing a vault operation.
---

# Obsidian CLI

The official Obsidian CLI (released in v1.12, February 2026) lets you control every aspect of Obsidian from the terminal. It communicates with a running Obsidian desktop instance via IPC.

> Read `references/command-reference.md` when you need specific flags, output formats, or
> subcommands for any command group. It covers all 130+ commands with full parameter tables
> and has a table of contents at the top.

## Prerequisites

| Requirement | Details |
|---|---|
| Obsidian Desktop | **v1.12.0+** |
| CLI enabled | Settings → Command line interface → Toggle ON |
| Obsidian running | The desktop app **must be running** for CLI to work (IPC) |

### Platform Notes

- **macOS / Linux**: The `obsidian` binary is registered in PATH automatically when you enable CLI in settings.
- **Windows**: Requires an `Obsidian.com` redirector file placed alongside `Obsidian.exe`. **Must run with normal user privileges** — admin terminals produce silent failures.
  - If colon subcommands (`property:set`, `daily:append`, etc.) with parameters return exit 127, check that `Obsidian.com` exists alongside `Obsidian.exe`. If missing, you have an outdated installer — download the latest from [obsidian.md/download](https://obsidian.md/download) and reinstall.
  - **Git Bash / MSYS2 users**: Bash resolves `obsidian` to `Obsidian.exe` (GUI) instead of `Obsidian.com` (CLI), causing colon+params to fail with exit 127 even when `Obsidian.com` is present. Create a wrapper script — see Troubleshooting.
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

The CLI provides **130+ commands** across these groups:

| Group | Key Commands | Purpose |
|---|---|---|
| **files** | `read`, `create`, `append`, `prepend`, `move`, `rename`, `delete`, `files`, `folders`, `file`, `random` | Note CRUD and file discovery |
| **daily** | `daily`, `daily:read`, `daily:append`, `daily:prepend`, `daily:path` | Daily note operations |
| **search** | `search`, `search:context` | Full-text search; `search:context` returns matching lines |
| **properties** | `properties`, `property:read`, `property:set`, `property:remove`, `aliases` | Frontmatter/metadata management |
| **tags** | `tags`, `tag` | Tag listing, counts, and filtering |
| **tasks** | `tasks`, `task` | Task querying, filtering, and toggling |
| **links** | `backlinks`, `links`, `unresolved`, `orphans`, `deadends` | Graph and link analysis |
| **bookmarks** | `bookmarks`, `bookmark` | List and add bookmarks |
| **templates** | `templates`, `template:read`, `template:insert` | Template listing, rendering, insertion |
| **plugins** | `plugins`, `plugin`, `plugin:enable`, `plugin:disable`, `plugin:install`, `plugin:uninstall`, `plugins:restrict` | Plugin management |
| **sync** | `sync`, `sync:status`, `sync:history`, `sync:read`, `sync:restore`, `sync:deleted` | Obsidian Sync operations |
| **themes** | `themes`, `theme`, `theme:set`, `theme:install`, `theme:uninstall` | Theme management |
| **snippets** | `snippets`, `snippets:enabled`, `snippet:enable`, `snippet:disable` | CSS snippet management |
| **commands** | `commands`, `command`, `hotkeys`, `hotkey` | Execute Obsidian commands by ID; inspect hotkeys |
| **bases** | `bases`, `base:query`, `base:views`, `base:create` | Obsidian Bases (v1.12+ database feature) |
| **history** | `history`, `history:list`, `history:read`, `history:restore` | File version recovery (File Recovery plugin) |
| **workspace** | `workspace`, `tabs`, `tab:open` | Workspace layout and tab management |
| **diff** | `diff` | Compare local vs sync file versions |
| **dev** | `eval`, `dev:screenshot`, `dev:debug`, `dev:console`, `dev:errors`, `dev:css`, `dev:dom`, `dev:mobile`, `devtools` | Developer/debugging tools |
| **vault** | `vault`, `vaults`, `version`, `reload`, `restart` | Vault info and app control |
| **other** | `outline`, `wordcount`, `recents` | Utility commands |

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
obsidian search query="meeting" format=json   # Returns JSON array of file paths
obsidian search query="urgent" case
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
obsidian tasks                          # All tasks (done + todo) — same as tasks all in v1.12
obsidian tasks all                      # All tasks (done + todo)
obsidian tasks done                     # Completed only
obsidian tasks daily                    # Tasks in today's daily note
obsidian task path="note.md" line=12 toggle
obsidian tasks | grep "\[ \]"           # Workaround: filter to incomplete only
```

### Developer & Automation

```bash
obsidian eval code="app.vault.getFiles().length"
obsidian dev:screenshot path="folder/screenshot.png"  # Path must be vault-relative
obsidian dev:debug on                                 # Required before dev:console
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
obsidian tasks | grep "\[ \]"          # Incomplete tasks across vault
obsidian orphans                        # Notes needing integration
obsidian unresolved                     # Broken links to fix
```

### Search and Extract for AI Processing

```bash
obsidian search query="meeting notes" format=json | jq '.[]'
obsidian read path="meetings/standup.md" | grep "Action item"
```

### Sync Management

```bash
obsidian sync:status                    # Check sync health
obsidian sync:history path="important.md"  # Version history
obsidian sync:restore path="important.md" version=3  # Rollback
```

### Execute Obsidian Commands

```bash
# Find a command ID, then execute it
obsidian commands | grep "graph"
obsidian command id="graph:open"

# Open settings, trigger a plugin action
obsidian command id="app:open-settings"
obsidian command id="dataview:dataview-force-refresh-views"
```

## Tips

1. **Paths are vault-relative** — use `folder/note.md`, not absolute filesystem paths.
2. **`create` paths omit `.md`** — the extension is added automatically.
3. **`move` requires full target path** including `.md` extension.
4. **Pipe-friendly** — plain text output works with `grep`, `awk`, `sed`, `jq`.
5. **JSON output** — use `format=json` on `search` for a JSON array of file paths. The `files` command does not support JSON output.
6. **Stderr noise** — GPU/Electron warnings on headless are harmless; filter with `2>/dev/null`.
7. **`daily:prepend`** inserts content after frontmatter, not at byte 0.
8. **Use `eval`** to run arbitrary JavaScript against the Obsidian API (`app.*`).
9. **`template:insert`** inserts into the currently active file in the Obsidian UI — it does not accept a `path=` parameter. If no file is open, it returns `Error: No active editor. Open a file first.` To create a file from a template via CLI, use `obsidian create path="..." template="..."` instead.
10. **`property:set` stores list values as strings** — `value="tag1, tag2"` writes a literal comma-separated string, not a YAML array. For proper array fields, edit the note's frontmatter directly (e.g. via `read` → modify → `create --force`) or use `eval` to call the Obsidian API.
11. **`eval` requires single-line JavaScript** — multiline JS passed inline fails with a token error. Write the script to a temp file instead:
    ```bash
    cat > /tmp/obs.js << 'JS'
    var files = app.vault.getMarkdownFiles();
    files.length;
    JS
    obsidian eval code="$(cat /tmp/obs.js)"
    ```
12. **Multi-vault targeting may not work in all environments** — `obsidian "My Vault" command` can return `Error: Command "My Vault" not found` on some setups. If this happens, omit the vault name (CLI targets the most recently active vault) and switch vaults manually in the Obsidian UI.
13. **When colon subcommands are unavailable** (e.g. Windows Git Bash without wrapper), prefer non-colon alternatives: use `properties` instead of `property:read`, and `obsidian daily:path` + `append` instead of `daily:append`.

## Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| Empty output / hangs | Obsidian not running, or admin terminal (Windows) | Start Obsidian; use normal-privilege terminal |
| Command not found | CLI not registered in PATH | Re-enable CLI in Settings; restart terminal |
| Unicode errors | Fixed in v1.12.2+ | Update Obsidian |
| Wrong vault targeted | Multi-vault ambiguity | Pass vault name as first arg |
| IPC socket not found (Linux) | `PrivateTmp=true` in systemd | Set `PrivateTmp=false` |
| Snap confinement issues | Snap restricts IPC | Use `.deb` package instead |
| Multi-vault `"Name" command` fails | Vault name matching issue | Omit vault name; target most recent vault |
| `property:set` list value is a string | CLI stores value as-is | Edit frontmatter directly or use `eval` |
| Colon+params exit 127 (missing `.com`) | Outdated installer — `Obsidian.com` absent | Reinstall from [obsidian.md/download](https://obsidian.md/download) |
| Colon+params exit 127 (Git Bash / MSYS2) | Bash resolves `obsidian` to `.exe` not `.com` | Create `~/bin/obsidian` wrapper: `#!/bin/bash` / `/c/path/to/Obsidian.com "$@"` and add `export PATH="$HOME/bin:$PATH"` to `~/.bashrc` |
