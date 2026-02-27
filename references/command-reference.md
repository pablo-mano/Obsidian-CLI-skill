# Obsidian CLI — Full Command Reference

Complete reference for all official Obsidian CLI commands (v1.12+).

**Syntax**: `obsidian [vault] <command> [subcommand] [key=value ...] [flags]`

All parameters use `key=value` syntax. Quote values containing spaces: `content="hello world"`.

---

## Table of Contents

1. [Files](#files)
2. [Daily Notes](#daily-notes)
3. [Search](#search)
4. [Properties](#properties)
5. [Tags](#tags)
6. [Tasks](#tasks)
7. [Links](#links)
8. [Bookmarks](#bookmarks)
9. [Templates](#templates)
10. [Plugins](#plugins)
11. [Sync](#sync)
12. [Themes](#themes)
13. [Developer](#developer)
14. [Vault & System](#vault--system)

---

## Files

File operations: read, write, create, move, delete, list.

### Reading Notes

```bash
obsidian read path="folder/note.md"
```

Prints raw markdown content of a note to stdout. Path is vault-relative.

### Creating Notes

```bash
obsidian create path="folder/note" content="# Title\n\nBody text"
obsidian create path="folder/note" template="template-name"
```

- Path should **not** include `.md` — it is appended automatically.
- Use `template=` to create from a template file.
- Use `content=` to set initial content directly.

### Appending & Prepending

```bash
obsidian append path="folder/note.md" content="Appended text"
obsidian prepend path="folder/note.md" content="Prepended text"
```

- `append` adds content at the end of the file.
- `prepend` adds content after the frontmatter (not at byte 0).

### Moving & Renaming

```bash
obsidian move path="old/path/note.md" name="new-name"
```

- `name=` is the new filename (moves/renames).
- The target path must include the `.md` extension when specifying a full path.

### Deleting

```bash
obsidian delete path="folder/note.md"           # Moves to trash
obsidian delete path="folder/note.md" permanent  # Permanent deletion
```

### File Discovery

```bash
obsidian files                       # List all files in vault
obsidian files ext=md                # Filter by extension
obsidian files folder="subfolder"    # Files in specific folder
obsidian files total                 # Just the file count
obsidian files format=json           # JSON output
obsidian folders                     # List all folders
obsidian file path="folder/note.md"  # File info (size, created, modified dates)
```

---

## Daily Notes

Operations on the daily note (requires Daily Notes core plugin enabled).

```bash
obsidian daily                           # Open today's daily note in Obsidian
obsidian daily:read                      # Print today's daily note content to stdout
obsidian daily:append content="text"     # Append content to today's note
obsidian daily:prepend content="text"    # Prepend content (after frontmatter)
```

**Notes:**
- `daily:prepend` inserts content after the frontmatter block, not at the very beginning.
- If today's note doesn't exist, `daily` will create it (using the configured template if set).
- Daily note format/folder are configured in Obsidian's Daily Notes plugin settings.

---

## Search

Full-text search across the vault.

```bash
obsidian search query="search text"
obsidian search query="text" path="folder"         # Scope to folder
obsidian search query="text" limit=10               # Limit results
obsidian search query="text" format=json            # JSON output
obsidian search query="text" matches                # Include match context/snippets
obsidian search query="text" case                   # Case-sensitive search
```

**Parameters:**
- `query=` — Search term (required)
- `path=` — Restrict search to a folder
- `limit=` — Maximum number of results
- `format=json` — Machine-readable JSON output
- `matches` — Show matching line context
- `case` — Enable case-sensitive matching

---

## Properties

Manage frontmatter (YAML metadata) on notes.

### Read All Properties

```bash
obsidian properties path="note.md"
```

### Read Single Property

```bash
obsidian property:read path="note.md" name="status"
```

### Set Property

```bash
obsidian property:set path="note.md" name="status" value="active"
obsidian property:set path="note.md" name="tags" value="[project, alpha]"
obsidian property:set path="note.md" name="date" value="2026-02-27"
```

### Remove Property

```bash
obsidian property:remove path="note.md" name="draft"
```

### Aliases

```bash
obsidian aliases path="note.md"
```

Lists all aliases defined in the note's frontmatter.

---

## Tags

Tag discovery and filtering.

```bash
obsidian tags                          # List all tags in the vault
obsidian tags counts                   # Tags with usage counts
obsidian tags counts sort=count        # Sorted by frequency (most used first)
obsidian tags path="note.md"           # Tags in a specific file
obsidian tag name="project/alpha"      # List notes with a specific tag
```

**Notes:**
- Nested tags are supported (e.g., `project/alpha`).
- Tags from both frontmatter and inline `#tag` syntax are included.

---

## Tasks

Query and manage checkbox tasks across the vault.

### Querying Tasks

```bash
obsidian tasks                         # All incomplete tasks
obsidian tasks all                     # All tasks (complete + incomplete)
obsidian tasks done                    # Only completed tasks
obsidian tasks path="note.md"          # Tasks in a specific file
obsidian tasks daily                   # Tasks in today's daily note
```

### Toggling Task Status

```bash
obsidian task path="note.md" line=12 toggle
```

Toggles the checkbox on the specified line number between `- [ ]` and `- [x]`.

---

## Links

Graph analysis and link management.

```bash
obsidian backlinks path="note.md"         # Notes linking TO this note
obsidian backlinks path="note.md" counts  # With link counts per file
obsidian links path="note.md"             # Outgoing links FROM this note
obsidian unresolved                        # All unresolved [[wikilinks]]
obsidian orphans                           # Notes with no incoming or outgoing links
obsidian deadends                          # Notes with no outgoing links
```

---

## Bookmarks

Manage Obsidian bookmarks (requires Bookmarks core plugin).

```bash
obsidian bookmarks                     # List all bookmarks
```

Bookmarks commands let you view and manage bookmarked notes, searches, and groups. Use `obsidian bookmarks --help` for all available subcommands.

---

## Templates

Work with note templates (requires Templates or Templater plugin).

```bash
obsidian templates                                      # List available templates
obsidian template:read name="weekly-review"             # Read template content
obsidian template:read name="weekly-review" resolve title="My Note"  # Render with variables
obsidian template:insert name="weekly-review"           # Insert template into active file
```

**Parameters:**
- `name=` — Template name (without path prefix or extension)
- `resolve` — Process template variables
- Title and other variables can be passed as `key=value` for template rendering.

---

## Plugins

Manage community and core plugins.

```bash
obsidian plugins                       # List all installed plugins
obsidian plugins:enabled               # Only enabled plugins
obsidian plugins versions              # Plugins with version numbers
obsidian plugin:enable id="canvas"     # Enable a plugin
obsidian plugin:disable id="canvas"    # Disable a plugin
obsidian plugin:install id="dataview"  # Install from community plugins
obsidian plugin:reload id="my-plugin"  # Reload a plugin (useful for dev)
```

---

## Sync

Manage Obsidian Sync (requires active Sync subscription).

```bash
obsidian sync                                   # Show sync status summary
obsidian sync on                                # Resume syncing
obsidian sync off                               # Pause syncing
obsidian sync:status                            # Detailed sync status
obsidian sync:history path="note.md"            # Version history for a file
obsidian sync:read path="note.md" version=3     # Read a specific version
obsidian sync:restore path="note.md" version=3  # Restore a previous version
obsidian sync:deleted                           # List files deleted via sync
```

---

## Themes

Manage appearance themes.

```bash
obsidian themes                        # List available/installed themes
```

Use `obsidian themes --help` for additional subcommands related to theme management (install, switch, etc.).

---

## Developer

Debugging and development tools.

### Screenshots

```bash
obsidian dev:screenshot path="screenshot.png"
```

Takes a screenshot of the Obsidian window and saves it.

### JavaScript Evaluation

```bash
obsidian eval code="app.vault.getFiles().length"
obsidian eval code="app.vault.getMarkdownFiles().map(f => f.path).join('\n')"
```

Executes arbitrary JavaScript in the Obsidian app context. Has access to the full Obsidian API (`app`, `app.vault`, `app.workspace`, `app.metadataCache`, etc.).

### Console & Errors

```bash
obsidian dev:console limit=20          # Recent console output
obsidian dev:errors                    # Recent error messages
```

---

## Vault & System

### Vault Information

```bash
obsidian vault                         # Current vault: name, path, file/folder counts
obsidian vaults                        # List all known vaults
```

### Other Utilities

```bash
obsidian version                       # Obsidian version info
obsidian outline path="note.md"        # Heading structure of a note
obsidian wordcount path="note.md"      # Word and character count
obsidian recents                       # Recently opened files
obsidian reload                        # Reload the vault (re-index)
```

---

## Output Formatting & Piping

The CLI outputs plain text by default, ideal for piping into Unix tools:

```bash
# Count notes in a folder
obsidian files folder="projects" | wc -l

# Find notes with a specific tag, then read them
obsidian tag name="urgent" | while read -r note; do
  echo "=== $note ==="
  obsidian read path="$note"
done

# Export search results as JSON and process with jq
obsidian search query="meeting" format=json | jq '.[].path'

# Filter console errors
obsidian dev:console limit=50 | grep -i error
```

---

## Multi-Vault Usage

When working with multiple vaults, pass the vault name as the **first argument** (before the command):

```bash
obsidian "Personal" daily:read
obsidian "Work" search query="standup"
obsidian "Archive" files total
```

If the vault name contains spaces, quote it. The vault name must match what's shown in `obsidian vaults`.

---

## Headless / Server Setup (Linux)

For running Obsidian CLI on a headless Linux server (useful for AI agent integration):

1. Install the `.deb` package (not snap — snap confinement breaks IPC)
2. Install and start `xvfb`: `Xvfb :5 -screen 0 1920x1080x24 &`
3. Start Obsidian under xvfb: `DISPLAY=:5 obsidian &`
4. Run CLI commands: `DISPLAY=:5 obsidian daily:read`

**Systemd note**: If running as a service, ensure `PrivateTmp=false` so the IPC socket is accessible.

**Stderr filtering**: Headless environments produce harmless GPU warnings. Filter with:

```bash
DISPLAY=:5 obsidian search query="test" 2>/dev/null
```
