---
name: folder-architect
description: >
  Creates routing structure for any project or client folder.
  Generates folder hierarchy + CLAUDE.md routers following the 4-layer
  context routing architecture.

  USE THIS SKILL WHEN:
  - User says "create folder structure", "set up project", "new project structure"
  - User says "folder architect", "create routing", "set up routing"
  - User says "onboard client" (for client folder creation)
  - User says "new client folder", "create client workspace"

  TRIGGERS: "folder architect", "folder structure", "create routing",
  "new project", "onboard client", "new client folder", "set up project"
---

# Folder Architect

Create self-routing folder structures for projects and clients. Every folder gets a CLAUDE.md router that tells AI what to load and when.

## Architecture Principles

1. **Routers under 80 lines.** Context files under 200 lines.
2. **1 file = 1 concern.** Never monoliths.
3. **Conditional loading.** "If task X, load Y." Never load everything.
4. **Entity > Domain > Global.** Specific rules override general ones.
5. **Human loops.** Never modify context files without approval.
6. **Append-only logs.** Decision logs never get entries deleted.

## Step 1: Gather Requirements

Ask the user:

1. **What is this for?** (project name, client name, or domain)
2. **What are the main work areas?** (e.g., marketing, product, operations)
3. **Who works on it?** (team members and roles)
4. **What are the key workflows?** (e.g., content writing, reporting, dev)
5. **Any special rules?** (compliance, approval flows, confidentiality)

If this is a **client folder**, also ask:
- Client status (active/inactive)
- Key products/services
- Platforms used (ad accounts, tools)
- Reporting cadence

## Step 2: Generate Folder Structure

Based on answers, create the folder tree. Standard patterns:

### Project Structure
```
project-name/
├── CLAUDE.md          ← Router
├── context/           ← What the project IS
│   ├── overview.md
│   ├── goals.md
│   └── stakeholders.md
├── work/              ← Active work areas (customize per project)
├── strategy/
│   ├── current-plan.md
│   └── decision-log.md
├── memory/
│   └── decisions/     ← Decision log storage
└── admin/             ← Contracts, billing (load only for admin tasks)
```

### Client Structure
```
client-name/
├── CLAUDE.md          ← Client router
├── context/
│   ├── client-info.md
│   ├── offer.md
│   ├── buyer-personas.md
│   ├── funnel.md
│   ├── accounts.md
│   └── brand-guidelines.md
├── strategy/
│   ├── current-plan.md
│   ├── decision-log.md
│   └── roadmap.md
├── campaigns/
│   └── change-log.md
├── reports/
├── copy/
├── data/
├── meetings/
├── memory/
│   └── decisions/     ← Decision log storage
└── admin/
    ├── contract.md
    ├── services.md
    ├── pricing.md
    └── onboarding.md
```

## Step 3: Write Routers

For each CLAUDE.md router, include:

1. **Identity line** what this folder is about
2. **Contents table** subfolder index
3. **Conditional loading rules** "If X task, load Y file"
4. **Rules** folder-specific constraints
5. **Calendar** (if applicable) reporting/review cadence

Keep under 80 lines. Route to subfolders, don't contain operational info.

## Step 4: Create Context Files

For each context file:
- Add YAML frontmatter (entity, domain, concern, last-updated, updated-by)
- One concern per file
- Under 200 lines
- Placeholder sections with clear instructions for what to fill in

## Step 5: Register in Parent Router

After creating the folder:
1. Add entry to the parent CLAUDE.md router table
2. If client: add to the clients domain router
3. Confirm with user before writing

## Output Format

Show the user:
1. Full folder tree (visual)
2. Each CLAUDE.md content (for approval)
3. List of context files with placeholder content
4. What was registered in parent routers

Wait for approval before creating anything.
