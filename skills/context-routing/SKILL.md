---
name: context-routing
description: >
  Information architecture framework for AI systems. Defines how to organize,
  segment, and serve context efficiently using router files and lazy loading.
  Use for: auditing workspace structure, adding new domains/clients/skills,
  designing workspaces from scratch, diagnosing context loading issues.
---

# Context Routing — Information Architecture Framework for AI

A system for organizing information where lightweight files (routers) direct an agent — AI or human — toward the relevant information for each task, instead of loading all context at once.

It works like a DNS system: each router resolves "where to go" based on the task. It doesn't contain the information — it points to it.

**Core principle: lazy loading.** Only read what's needed for the current task. This reduces noise, saves tokens, and improves response accuracy.

**Platform-agnostic.** This framework works in any system: a directory of markdown files, a web interface, a database, or an API. What matters is the pattern, not the implementation.

## The 7 Fundamental Principles

### 1. Lightweight files, never monoliths
A router never exceeds 80 lines. It doesn't contain extensive operational knowledge — it only points to it. If a file grows beyond 200 lines, split it.

### 2. Explicit authority hierarchy
When there's conflict between layers, the most specific wins:
```
Entity > Domain > Global > Skill > Reference
```
Example: if the global router says "formal tone" but the client router says "informal tone," the client wins.

### 3. Granularity by concern
Each file addresses ONE single concern: offer, funnel, pricing, brand. Never "everything about the client" in a single file.

### 4. Conditional routing by task
Loading rules are conditional: "if the task is X, load Y." Nothing loads by default. This is what differentiates a router from a simple index.

### 5. Explicit human loops
Every context modification requires documented human approval. Context files are the source of truth — they don't get changed silently.

### 6. Mandatory logging
Every decision or change is recorded in a dedicated log (decision-log, change-log). Logs are append-only: previous entries are never deleted.

### 7. Self-contained capsules
Skills and tools are self-contained: their definition file + references include everything needed to function. Client context is injected at runtime, not embedded in the skill.

## Anatomy of the Tree

Maximum 4 layers deep to reach any piece of information:

```
Layer 0: Global Router
  System identity + domain table + loading rules + security

Layer 1: Domains
  Each main area has its own router
  Example: agency/, clients/, skills/, tools/

Layer 2: Entities
  Each unit within a domain has its own router
  Example: clients/acme-corp/, skills/ad-reporting/

Layer 3: Granular context files
  Individual files with a single concern
  Example: context/offer.md, admin/pricing.md

Layer 4 (optional): Supporting references
  Support material within skills or tools
  Example: references/meta-ads.md, references/benchmarks.md
```

For details and commented examples: `references/anatomy-of-a-router.md`

## How to Write a Router

A router has 5 possible sections:

1. **Identity header** — what this domain/entity is (2-3 lines max)
2. **Subfolder/file table** — content map
3. **Conditional loading rules** — "for task X, read file Y" (the most important section)
4. **Reading priority** — order to consult sources if there's ambiguity
5. **Rules and restrictions** — security, conventions, human loops

What a router should NEVER contain:
- Extensive operational knowledge (that goes in granular files)
- Data that changes frequently (better to reference the file that contains it)
- Complete workflow logic (that's a skill, not a router)

For detailed structure and 3 examples: `references/anatomy-of-a-router.md`

## How to Write Context Files

Each context file follows these rules:
- **1 file = 1 concern** (offer, funnel, buyer personas, pricing)
- **Standard frontmatter** with metadata for indexing and auditing
- **50-200 lines** as target range. If it exceeds 200, consider splitting

Standard frontmatter:
```yaml
---
entity: [entity-slug]
domain: [domain: context, admin, strategy, etc.]
concern: [concern: offer, funnel, pricing, etc.]
last-updated: [YYYY-MM-DD]
updated-by: [name]
---
```

For detailed structure and examples: `references/anatomy-of-a-context-file.md`

## Loading Types

### Mandatory (ALWAYS)
Loaded before any work with that entity.
- Example: Always read the client router before doing any task for that client.

### Conditional (IF task = X)
Loaded only when the task requires it.
- Example: If the task is campaign analysis, load the ad-reporting skill.
- Example: If the task is billing, load client admin files + agency admin.

### On-demand
Loaded within a workflow, at the specific step that needs it.
- Example: The reporting skill reads the Meta benchmarks file only at the analysis step, not at the start.

For complete catalog of rule types: `references/routing-rules-catalog.md`

## Anti-patterns

The 7 most common mistakes when implementing context routing:

1. **The Monolith** — Putting everything in a single 500+ line file
2. **The Ghost** — Referencing files that don't exist
3. **Duplication** — Same information in the router AND in the granular file
4. **Total Load** — Loading all files for any task
5. **The Router-that-doesn't-route** — An index that describes content but doesn't say WHEN to read it
6. **Cross-Context** — Information from one entity visible in another's context
7. **Coupled Skill** — A skill that depends on external files to function

For examples and solutions for each anti-pattern: `references/anti-patterns.md`

## Audit Workflow

4 steps to validate workspace integrity. **Only diagnoses, NEVER deletes or modifies automatically.**

### Step 1: Router inventory
Walk the tree and list all router files (CLAUDE.md, SKILL.md, README.md or equivalents in other systems).

### Step 2: Link validation
For each path referenced in a router, verify the file or folder exists. Report "broken links" (ghosts).

### Step 3: Orphan detection
Files that exist but aren't referenced from any router. Could be forgotten files or pending integration.

### Step 4: Consistency check
Compare templates with actual instances. Verify entities follow the template structure.

The output is a health report, not an automatic action. The decision to create, move, or delete files is always human.

For executable checklist: `references/audit-checklist.md`

## Scaling

### New client
1. Copy the client template (_template/)
2. Fill placeholders with new client data
3. Register in the domain router (clients/) and in the global router

### New skill
1. Create folder with SKILL.md + references/
2. Register in the skills router
3. Add conditional loading rule to the global router

### New domain
1. Create root folder with its own router
2. Register in the global router (structure table + loading rule)

For detailed step-by-step with checklists: `references/scaling-playbook.md`

## Naming Conventions

- **Routers:** CLAUDE.md (or system equivalent: SKILL.md for skills, README.md for tools)
- **Context files:** kebab-case.md, always singular (offer.md, not offers.md)
- **Logs:** suffix -log.md (decision-log.md, change-log.md)
- **Entity slugs:** kebab-case, no accents (juan-vidal, not Juan Vidal)
- **Reports:** [type]-[YYYY-MM-DD].md or .html

## Hard Rules

1. A router never exceeds 80 lines
2. A context file never exceeds 200 lines without justification
3. Every referenced path must exist — zero broken links tolerated
4. Every context modification requires explicit human approval
5. Skills are self-contained capsules — with their definition + references
6. Frontmatter with `last-updated` and `updated-by` on every context file
7. Logs are append-only — previous entries are never deleted
8. Authority hierarchy is: Entity > Domain > Global > Skill > Reference

## Web Implementation

For a guide on implementing this framework in a web application (data model, context injection, auto-update): `references/web-implementation-spec.md`
