---
name: getting-started
description: >
  Guided setup for context routing in a new workspace. Walks the user
  through creating their first routed folder structure, explains the
  framework, and connects all plugin skills.

  USE THIS SKILL WHEN:
  - User says "set up context routing", "get started", "initialize workspace"
  - User says "how does context routing work", "explain the framework"
  - User just installed the plugin and needs orientation
  - User says "context routing", "start routing"

  TRIGGERS: "get started", "set up context routing", "initialize workspace",
  "context routing setup", "start routing"
---

# Getting Started with Context Routing

Welcome. This skill walks you through setting up context routing in your workspace. By the end, you'll have a self-routing folder structure where AI loads only what it needs for each task.

## What You're Setting Up

Context routing is a system where lightweight files (CLAUDE.md routers) direct AI to the right information for each task. Instead of dumping everything into context, routers use conditional rules: "if the task is X, load Y."

**The result:** Less noise, fewer wasted tokens, better AI responses.

## Step 1: Understand Your Workspace

Ask the user:

1. **What is this workspace for?** (agency, company, personal projects, freelance)
2. **What are your main work areas?** (e.g., clients, marketing, product, operations)
3. **How many clients/projects do you manage?** (helps determine if you need a clients domain)
4. **What tools do you use with AI?** (Claude Code, Claude chat, API, other)

## Step 2: Create the Global Router

Based on the answers, create a root `CLAUDE.md` that:

1. States the workspace identity (2-3 lines)
2. Lists the main domains as a table
3. Sets conditional loading rules ("for task X, load domain Y")
4. Defines security rules (data isolation, secrets in .env only)
5. Sets conventions (naming, logging, human approval)

**Reference the framework:** Read `skills/context-routing/SKILL.md` for the 7 principles and router anatomy. Share the key principles with the user as you build.

## Step 3: Create Domain Routers

For each main work area:

1. Create the domain folder
2. Create a `CLAUDE.md` inside it with:
   - What this domain contains
   - Subfolder structure
   - Domain-specific loading rules
   - Domain-specific rules

Use the **folder-architect** skill for complex domains. Trigger: "folder architect"

## Step 4: Set Up Your First Entity

Pick one client or project to set up fully:

1. Use the **folder-architect** skill to create the entity structure
2. Fill in the context files (start with client-info.md and offer.md)
3. Add frontmatter to each file
4. Register in the domain router

Show the user how conditional loading works by example:
- "When you ask me to write copy for this client, I'll load offer.md and buyer-personas.md"
- "When you ask about billing, I'll load admin/contract.md instead"

## Step 5: Explain the Maintenance Skills

Tell the user about the other skills in this plugin:

1. **drive-hygiene** — "Say 'clean up' or 'hygiene' to scan for duplicates, orphans, phantom references, and clutter. I'll walk you through each finding."

2. **update-rules** — "Say 'update rules' or 'rule audit' to review all your CLAUDE.md routers. I'll check for conflicts, outdated references, and rules that may no longer apply."

3. **folder-architect** — "Say 'folder architect' or 'new project' anytime you need to create a new routed folder structure."

## Step 6: Verify the Setup

Run a quick health check:

1. List all CLAUDE.md files created
2. Verify each referenced path exists (no ghosts)
3. Test one conditional loading rule by simulating a task
4. Show the user the full tree structure

## Output

At the end, show:
1. The complete folder tree with all routers
2. Summary of what was created
3. Quick reference card of available commands:

```
COMMANDS:
- "folder architect"  → Create new routed folder structures
- "clean up"          → Scan for clutter and fix it
- "update rules"      → Audit all routers for conflicts
- "context routing"   → Re-run this setup guide
```

## Rules

- Never create files without showing the user first and getting approval
- Explain WHY each principle matters as you go (don't just build silently)
- Start simple — one global router + one domain + one entity is enough to get started
- The user can always add more domains and entities later using folder-architect
