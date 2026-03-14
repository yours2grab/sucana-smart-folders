# Anatomy of a Router

A router is a lightweight file that directs the agent toward relevant information. It doesn't contain operational knowledge — it only points to it.

## The 5 Sections of a Router

### 1. Identity header (2-3 lines)
What this domain or entity is. Answers: "where am I and what's here."

### 2. Subfolder/file table
Content map. Lists what each relevant subfolder or file contains.

### 3. Conditional loading rules
**The most important section.** Defines WHEN to load each resource:
- "For task X, read file Y"
- "ALWAYS read Z before any work"

### 4. Reading priority
Order to consult sources when there's ambiguity or it's unclear what to load.

### 5. Rules and restrictions
Security, naming conventions, human loops, confidential data.

## Example 1: Global Router (Layer 0)

This is the highest-level router. Defines the identity of the entire system.

```markdown
# [System Name]

## Identity
[2-3 lines: what this system is, who operates it, what it's for]

## Workspace Structure
| Folder | Contents |
|--------|----------|
| agency/ | Knowledge base: processes, strategy, benchmarks |
| clients/ | One workspace per client with context and operations |
| skills/ | Reusable, client-agnostic capabilities |
| tools/ | Automation tools |

## Context Loading Rules                    <-- KEY SECTION
1. ALWAYS read the client router before working for that client
2. For campaign analysis: load skills/ad-reporting/SKILL.md
3. For copywriting: load skills/copywriter/SKILL.md
4. For strategy: read agency/strategy/ files
5. For billing: read clients/[client]/admin/ + agency/admin/

## Modification Rules
- NEVER modify client context without explicit approval
- Record every change in the corresponding log

## Security
- NEVER expose tokens, secrets, or credentials
- Each client's data is CONFIDENTIAL between clients

## Conventions
- Reports: clients/[client]/reports/[type]-[YYYY-MM-DD]
- Copy: clients/[client]/copy/
- Decisions: clients/[client]/strategy/decision-log.md
```

**Why it works:**
- Identity in 2 lines — doesn't extend
- Structure table gives a quick overview
- Loading rules are CONDITIONAL — each starts with "for [task]"
- Security and conventions are compact

## Example 2: Domain Router (Layer 1)

This router lives within a domain (e.g., clients/) and defines rules for all entities in that domain.

```markdown
# Client Management

Each subfolder represents a client with their complete workspace.

## Per-Client Structure
Each client folder follows the _template/ structure:
- CLAUDE.md — Specific instructions (ALWAYS READ before working)
- context/ — Client info, accounts, brand, audience, offer, funnel
- strategy/ — Active plan, roadmap, decision log
- campaigns/ — Active campaigns, archived, change log
- admin/ — Contract, billing, services, pricing
- [other operational folders]

## Rules                                    <-- DOMAIN RULES
1. Always read the client router before starting
2. Never mix data between clients
3. Record decisions in strategy/decision-log.md
4. Record campaign changes in campaigns/change-log.md

## Adding a New Client
Copy _template/ to a new folder with the client slug.
```

**Why it works:**
- Explains the structure that ALL entities in the domain share
- Rules apply to any client, not a specific one
- Includes the scaling operation (adding new client)

## Example 3: Entity Router (Layer 2)

This router lives within a specific entity (e.g., a client) and is the entry point for working with it.

```markdown
# Client: Acme Corp
**Slug:** acme-corp
**Status:** Active
**Start date:** 2026-01-15

## Quick Reference
| Field | Value |
|-------|-------|
| Monthly budget | $5,000 |
| Target CPL | <$15 |
| Current CPL | ~$12 |
| Sector | B2B SaaS |

## Context Files                            <-- MANDATORY LOADING
Read these files before doing any work:
1. context/client-info.md — General info and contacts
2. context/offer.md — Product, price, USP
3. context/buyer-personas.md — Audience profiles
4. context/funnel.md — Funnel structure and URLs
5. context/accounts.md — Ad account IDs

## Administrative Files                      <-- CONDITIONAL LOADING
For questions about contract, billing, or admin:
1. admin/contract.md
2. admin/pricing.md
3. admin/invoicing.md

## Active Strategy
See strategy/current-plan.md

## Client-Specific Rules                     <-- ENTITY RULES
- Regulated sector: never promise guaranteed results
- Professional but approachable tone
- Mandatory disclaimer in ads
```

**Why it works:**
- Quick reference gives operational context without opening other files
- Context files are MANDATORY (always loaded)
- Admin files are CONDITIONAL (only if the task is administrative)
- Specific rules override domain/global rules (authority hierarchy)

## Golden Rules for Routers

1. **Maximum 80 lines** — if you need more, you're putting content that should go in granular files
2. **Every loading rule starts with a condition** — "for X", "if Y", "always Z"
3. **Don't repeat information** — the router points, doesn't duplicate
4. **Update the router when paths change** — a broken link is a ghost
5. **Keep specific rules separate from global ones** — specific rules go in the entity router, global rules in the domain or global router
