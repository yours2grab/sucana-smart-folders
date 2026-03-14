# Context Routing Plugin

**Stop feeding AI everything. Start telling it where to look.**

## The Problem

If you use Claude Code with more than a handful of files, you've probably hit this: you put all your instructions in one giant CLAUDE.md. It gets to 500 lines. Then 1,000. Then 2,000. Claude starts ignoring half of it. It loads your billing info when you asked it to write a blog post. It wastes tokens on stuff that has nothing to do with the task.

You can't blame the AI. You gave it everything and hoped it would figure out what mattered.

## What This Plugin Does

Context Routing fixes this by adding a simple layer between your files and your AI: **routers**.

A router is a small file (under 80 lines) that says: "If the task is marketing, go read the marketing folder. If it's billing, go read admin. If it's a specific client, load their context first."

Think of it like a GPS for your AI. Instead of handing it a map of the entire country, you give it turn-by-turn directions for the specific trip it's on.

Here's what that looks like in practice:

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

Each CLAUDE.md is a router. Each one is small. AI reads the root router, figures out what kind of task you're doing, and follows the trail to load only what it actually needs. That's it.

## Before and After

**Before:** AI gets a 2,000-line CLAUDE.md with everything. Wastes tokens. Ignores half of it. Gets confused by your billing info while trying to write ad copy.

**After:** AI reads a 40-line root router → sees "this is a marketing task" → loads Marketing/CLAUDE.md (30 lines) → loads only seo-plan.md. Total context: 120 lines of exactly what it needs. Faster. Sharper. No noise.

## Installation

### Step 1: Download
Grab the `context-routing.plugin` file from this repo (it's in the root).

### Step 2: Place it
Drop it in one of these locations:
- **Your project's root directory** — plugin loads for that project only
- **`~/.claude/plugins/`** — plugin loads for every project

### Step 3: Start Claude Code
That's it. The plugin loads automatically. No config needed.

### Step 4: Say "get started"
Type this in Claude Code:

```
get started with context routing
```

A guided setup walks you through:
1. What your workspace is (agency, company, personal projects)
2. Creating your first root router
3. Setting up your first domain and entity
4. How to maintain it going forward

## What You Can Do With It

Once installed, you have 4 commands:

| Say this | What happens |
|----------|-------------|
| **"get started"** | Guided first-time setup. Builds your routing structure from scratch. |
| **"folder architect"** | Creates a new routed folder for a project or client. Generates the router, context files, and registers it in the parent. |
| **"clean up"** or **"hygiene"** | Scans your workspace for mess: duplicate files, broken references, orphan files nobody uses, empty folders, clutter. Walks you through each finding one by one. |
| **"update rules"** or **"rule audit"** | Reads every router in your workspace. Finds conflicts between them. Checks if rules are still valid. Lets you update or remove them. |

## What's Inside the Plugin

### 5 Skills
The plugin bundles 5 skills that work together:

- **Getting Started** — The guided setup. Where everyone begins.
- **Context Routing** — The core framework. 7 principles, 4-layer architecture, naming conventions, hard rules. This is the brain.
- **Folder Architect** — Creates new folder structures with routers already wired up. Works for projects, clients, or any domain.
- **Drive Hygiene** — An 8-pattern scanner that finds duplicates, ghost references, orphan files, and clutter. Never deletes anything without asking.
- **Update Rules** — Audits every CLAUDE.md router in your workspace. Catches conflicts, outdated references, and rules that contradict each other.

### 7 Reference Docs
Deep documentation if you want to understand the framework or customize it:

- **Anatomy of a Router** — How to write routers, with 3 real examples at different levels
- **Anatomy of a Context File** — How to structure the files that routers point to
- **Anti-patterns** — 7 common mistakes and how to fix them
- **Routing Rules Catalog** — The 5 types of loading rules (mandatory, conditional, on-demand, etc.)
- **Scaling Playbook** — Step-by-step for adding new clients, skills, or entire domains
- **Audit Checklist** — 6-step workspace health check you can run anytime
- **Web Implementation Spec** — Technical spec if you want to build this as a product

## The 7 Principles

These are the rules the entire system follows:

1. **Small files, never monoliths.** Routers stay under 80 lines. Context files under 200. If it's longer, split it.
2. **Specific beats general.** If the client router says "casual tone" but the global router says "formal," the client wins. Always.
3. **One file, one job.** A file covers one thing: the offer, the funnel, the pricing. Never "everything about the client" in one file.
4. **Load only what you need.** Every loading rule starts with a condition: "If the task is X, load Y." Nothing loads by default unless it truly applies to every task.
5. **Humans approve changes.** Context files are your source of truth. AI proposes changes, you approve them. Nothing gets modified silently.
6. **Log everything.** Every decision gets recorded. Logs are append-only. You never delete history.
7. **Skills are self-contained.** A skill carries everything it needs inside its own folder. No external dependencies that break when you move things around.

## FAQ

**Does this work with any Claude Code project?**
Yes. It's just markdown files and folder structure. Works with code repos, file-based workspaces, Google Drive folders, anything.

**What if I already have a CLAUDE.md?**
The getting-started skill will help you break it apart. Your existing rules get preserved and distributed across the right routers. Nothing gets lost.

**How deep can the folder structure go?**
4 layers max: Global → Domain → Entity → Context file. That's enough for any workspace. If you need more than 4, something's overengineered.

**Can I use this for client work?**
Yes. There's a full client template with context files (offer, personas, funnel), admin files (contract, pricing), campaign tracking, and data isolation rules so client info never crosses over.

**Do I need to set everything up at once?**
No. Start with one root router and one domain. Add more as you go. The system works with partial information. The audit skill will tell you what's missing.

## Credits

Built by Virgil Brewster and Victor at [Sucana](https://sucana.ai).
Framework architecture by Victor.

## License

MIT
