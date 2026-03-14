---
name: update-rules
description: >
  Review and update all CLAUDE.md router rules across the drive.
  Reads every router, asks if each rule is still valid, detects
  conflicts between routers, and updates in place.

  USE THIS SKILL WHEN:
  - User says "update rules", "review rules", "check rules"
  - User says "are my rules current", "rule audit", "router audit"
  - User says "fix conflicting rules", "rule conflicts"

  TRIGGERS: "update rules", "review rules", "check rules",
  "rule audit", "router audit", "update routers"
---

# Update Rules

Review every CLAUDE.md router in the drive. Validate rules. Fix conflicts. Update in place.

## Step 1: Collect All Routers

Find every CLAUDE.md in the workspace:
```
find [workspace-root] -name "CLAUDE.md" -not -path "*/_archive/*" -not -path "*/node_modules/*"
```

Sort by layer (root first, then Layer 1, then Layer 2, etc.).

## Step 2: Extract Rules

For each CLAUDE.md, extract:
- **Routing rules** (conditional loading: "if X, load Y")
- **Behavioral rules** (style, format, workflow constraints)
- **Security rules** (data isolation, secrets, approvals)
- **References** (paths to files/folders mentioned)

Build a combined rules table:

| Rule | Source Router | Layer |
|------|-------------|-------|
| ... | ... | ... |

## Step 3: Conflict Detection

Check for conflicts across routers:

1. **Same topic, different rules** e.g., two routers say different things about voice file location
2. **Redundant rules** same rule repeated in multiple routers (should only be in one place per hierarchy)
3. **Outdated references** paths that no longer exist
4. **Hierarchy violations** Layer 2 router contradicting Layer 1 without clear reason

Present each conflict:
```
CONFLICT: [topic]
  Router A (Layer X): [rule text]
  Router B (Layer Y): [rule text]
  Suggestion: [which to keep and why]
```

## Step 4: Rule Review

For each rule, read it back to the user and ask:

"[Rule text]"
Still valid? (Yes / Update / Remove)

If **Update**: ask what the new rule should be, update in place.
If **Remove**: delete the rule from the router.
If **Yes**: move on.

Go through rules grouped by router, starting from Layer 0 (root) down.

## Step 5: Apply Changes

For each approved change:
1. Edit the CLAUDE.md in place
2. Show the diff
3. Log the change

## Step 6: Log Everything

Log to the nearest `memory/decisions/` folder. If none exists, create one at the workspace root as `memory/decisions/[date]-rule-update.md`.

```
# Rule Update [date]

## Changes Made

| Router | Rule | Action | Old | New |
|--------|------|--------|-----|-----|

## Conflicts Resolved

| Topic | Resolution |
|-------|-----------|

## Rules Confirmed Valid
[count] rules across [count] routers confirmed current.
```

## Rules for This Skill

- Never change a rule without asking first
- Show the exact text being changed and the proposed replacement
- If a rule exists in multiple places, consolidate to the highest appropriate layer
- Root router rules apply everywhere unless explicitly overridden by a lower layer
- Log every change, even "confirmed valid"
