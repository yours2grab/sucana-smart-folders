---
name: drive-hygiene
description: >
  Drive cleanup and hygiene scanning. Detects duplicates, orphans,
  phantom references, empty dirs, clutter, and loose files.
  Walks through findings with user. Logs all decisions.

  USE THIS SKILL WHEN:
  - User says "clean up", "hygiene", "scan drive", "audit files"
  - User says "find duplicates", "find orphans", "check for clutter"
  - User says "drive cleanup", "folder cleanup", "file cleanup"
  - At session end for folder sweep

  TRIGGERS: "hygiene", "clean up", "cleanup", "scan drive",
  "find duplicates", "audit files", "drive cleanup", "folder sweep"
---

# Drive Hygiene

Scan for clutter, walk through findings one by one, log every decision. Never auto-delete.

## 8-Pattern Scan

Run all 8 checks. Report findings grouped by pattern.

### 1. Duplicate Files
Search for files with (1), (2) suffixes, -backup, -archive, -old, -new in names.
```
find [path] -name "* (1)*" -o -name "* (2)*" -o -name "*-backup*" -o -name "*-archive*" -o -name "*-old*" -o -name "*-new*"
```

### 2. Empty Directories
```
find [path] -type d -empty -not -path "*/_archive/*" -not -path "*/node_modules/*"
```

### 3. "Copy of" Files
Google Drive auto-copies.
```
find [path] -name "Copy of *"
```

### 4. Orphan SKILL Files
.skill or SKILL.md files not referenced by any CLAUDE.md router. Grep all CLAUDE.md files for each skill name.

### 5. Phantom References
Paths mentioned in CLAUDE.md or SKILL.md that don't exist on disk. For each CLAUDE.md, extract referenced paths and verify they exist.

### 6. Nested Duplicates
Folders that duplicate their parent structure (e.g., Sucana/Sucana/, Business/Business/).
```
find [path] -type d | grep -E '/([^/]+)/\1$'
```

### 7. Claude-Created Orphans
Files with names suggesting temp/generated content: Untitled document, _tmp_, draft artifacts not referenced anywhere.

### 8. Loose Files at Folder Roots
Files sitting at a folder's root level instead of in a subfolder. Exceptions: CLAUDE.md, .DS_Store, config files (.nano-banana-config.json, *.plugin).

## Cleanup Flow

For EACH finding:

1. **Show:** What file, where it is, why it's flagged
2. **Show:** What happens if removed (any references? any skills that use it?)
3. **Ask:** Keep / Archive to _archive/YYYY-MM-DD/ / Delete
4. **Execute:** The chosen action
5. **Log:** Decision to the nearest `memory/decisions/` folder. If none exists, create one at the workspace root as `memory/decisions/[date]-hygiene.md`.

Rules:
- Never auto-delete. Never batch-delete without showing each item.
- Always check skill references before moving/deleting any file.
- Archive goes to _archive/YYYY-MM-DD/filename.
- Archived files are recoverable.

## Session-End Folder Sweep

Quick scan of folders touched during the session:

1. Find loose files at folder roots (not CLAUDE.md or config)
2. Show which files are loose and where
3. Suggest which subfolder each belongs in
4. Ask user to approve moves
5. Move approved files

## Archive Management

When _archive/ has files older than 30 days:
- Prompt: "X files in _archive/ older than 30 days. Review for permanent deletion?"
- Show each file with its archive date
- Ask: Keep / Delete permanently

## Report Format

```
## Drive Hygiene Report [date]

### Findings
| # | Pattern | File | Location | Action |
|---|---------|------|----------|--------|

### Summary
- Scanned: [X] directories
- Found: [X] issues
- Resolved: [X] (deleted: Y, archived: Z, kept: W)

### Decisions logged to: memory/decisions/[date]-hygiene.md
```
