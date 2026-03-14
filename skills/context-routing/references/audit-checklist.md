# Workspace Audit Checklist

Run periodically to validate the integrity of the context routing structure.
**Only diagnoses — NEVER deletes or modifies automatically.**

## Step 1: Router Inventory

- [ ] List all router files in the workspace (CLAUDE.md, SKILL.md, README.md)
- [ ] Verify each domain (level 1 folder) has its own router
- [ ] Verify each active entity (client, skill, tool) has its own router
- [ ] Verify no router exceeds 80 lines

## Step 2: Link Validation

For each path referenced in a router:

- [ ] The referenced file or folder EXISTS
- [ ] If it's a folder, it's NOT empty (or is marked as pending fill)
- [ ] If the file exists, its content is consistent with what the router says it contains

**Common ghosts:**
- Process files referenced but not created
- Strategy or benchmark folders empty but referenced as if they have content
- References to templates that have changed name or location

## Step 3: Orphan Detection

- [ ] Search for .md files not referenced from any router
- [ ] For each orphan, decide:
  - Integrate: add it to the corresponding router
  - Archive: move it to an archive folder
  - Keep: if it's a temporary work file

## Step 4: Template Consistency

- [ ] Compare `_template/` with each active client/entity
- [ ] For each entity, verify:
  - [ ] Has CLAUDE.md
  - [ ] Has all template subfolders (context/, admin/, strategy/, campaigns/)
  - [ ] Mandatory files exist (even if empty)
- [ ] Document differences between template and instances (extra folders, missing files)

## Step 5: Context Freshness

- [ ] Check `last-updated` frontmatter on all context files
- [ ] List files not updated in more than 30 days
- [ ] List files missing frontmatter (candidates to add)

## Step 6: Loading Rules Validation

- [ ] Global router has conditional loading rules (not just an index)
- [ ] Each domain router has area-specific rules
- [ ] Skills registered in the global router match the folders in skills/
- [ ] Clients registered in the global router match the folders in clients/

## Report Format

The audit output should be a markdown file with this structure:

```markdown
# Workspace Health Report — [YYYY-MM-DD]

## Summary
- Total routers: X
- Total context files: X
- Ghosts detected: X
- Orphans detected: X
- Files without frontmatter: X
- Stale files (>30 days): X

## Ghosts (broken links)
| Router | Referenced path | Status |
|--------|----------------|--------|
| CLAUDE.md (root) | agency/processes/ad-policy-compliance.md | Does not exist |

## Orphans (not referenced)
| File | Location | Suggestion |
|------|----------|------------|
| [file] | [path] | Integrate / Archive |

## Template Inconsistencies
| Entity | Missing | Has extra |
|--------|---------|-----------|
| [slug] | CLAUDE.md, context/ | landings/ |

## Freshness
| File | Last updated | Days since update |
|------|-------------|------------------|
| [path] | [date] | [N] |
```
