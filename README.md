# Context Routing Plugin

A Claude Code plugin that turns any folder into a self-routing AI workspace. Routers (CLAUDE.md files) tell AI what to load and when, so it only reads what's needed for each task.

## What This Does

Instead of dumping all your files into AI context, this plugin creates a routing layer:

```
Your workspace/
├── CLAUDE.md              ← "For marketing tasks, load Marketing/"
├── Marketing/
│   ├── CLAUDE.md          ← "For SEO, load seo-plan.md"
│   ├── seo-plan.md
│   └── content-ideas.md
├── Clients/
│   ├── CLAUDE.md          ← "Always read the client router first"
│   └── acme-corp/
│       ├── CLAUDE.md      ← "For billing, load admin/"
│       ├── context/
│       └── admin/
└── Skills/
    └── CLAUDE.md
```

Each router is under 80 lines. Each context file covers one concern. AI loads only what the task requires.

## Installation

1. Download the `context-routing.plugin` file
2. Place it in your project's root directory, or in `~/.claude/plugins/`
3. Start Claude Code — the plugin loads automatically

## Getting Started

After installation, say:

```
get started with context routing
```

This triggers a guided setup that:
1. Asks about your workspace (agency, company, personal)
2. Creates your global router (CLAUDE.md)
3. Sets up your first domain and entity
4. Explains how to maintain it

## Available Commands

| Command | What it does |
|---------|-------------|
| `get started` | Guided workspace setup |
| `folder architect` | Create a new routed folder structure |
| `clean up` / `hygiene` | Scan for duplicates, orphans, broken links |
| `update rules` / `rule audit` | Review all routers for conflicts |

## What's Inside

### 5 Skills

| Skill | Purpose |
|-------|---------|
| **getting-started** | Guided first-time setup |
| **context-routing** | The core framework (7 principles, 4-layer architecture) |
| **folder-architect** | Create new project/client folder structures |
| **drive-hygiene** | 8-pattern cleanup scan |
| **update-rules** | Audit and fix all CLAUDE.md routers |

### 7 Reference Docs

Deep documentation for the framework:

- **anatomy-of-a-router.md** — How to write routers with 3 examples
- **anatomy-of-a-context-file.md** — How to write context files with frontmatter
- **anti-patterns.md** — 7 mistakes to avoid (with fixes)
- **routing-rules-catalog.md** — 5 types of loading rules
- **scaling-playbook.md** — How to add clients, skills, domains
- **audit-checklist.md** — 6-step workspace health check
- **web-implementation-spec.md** — Technical spec for building this as a web product

## The 7 Principles

1. **Lightweight files, never monoliths** — Routers < 80 lines. Context files < 200 lines.
2. **Explicit authority hierarchy** — Entity > Domain > Global. Specific wins.
3. **Granularity by concern** — 1 file = 1 concern. Never "everything about X."
4. **Conditional routing** — "If task X, load Y." Not "load everything."
5. **Human loops** — Context files don't change without approval.
6. **Mandatory logging** — Decisions logged. Append-only.
7. **Self-contained skills** — Skills carry their own references. No external dependencies.

## Example: Before and After

**Before (no routing):**
AI gets a 2,000-line CLAUDE.md with everything. Wastes tokens. Ignores half of it. Gets confused.

**After (with routing):**
AI reads a 40-line root router → sees "this is a marketing task" → loads Marketing/CLAUDE.md (30 lines) → loads only seo-plan.md. Total context: 120 lines of exactly what it needs.

## FAQ

**Q: Does this work with any Claude Code project?**
Yes. It's just markdown files and folder structure. Works with any codebase or file-based workspace.

**Q: What if I already have a CLAUDE.md?**
The getting-started skill will help you restructure it. Your existing rules get preserved and distributed across the right routers.

**Q: How many layers deep can it go?**
4 layers max (Global → Domain → Entity → Context file). Anything deeper and you're overcomplicating it.

**Q: Can I use this for client work?**
Yes. The client template structure includes context files, admin files, campaign tracking, and data isolation rules.

## Credits

Built by Virgil Brewster and Victor at Sucana.
Framework architecture by Victor.

## License

MIT
