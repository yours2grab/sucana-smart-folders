# Context Routing Anti-patterns

The 7 most common mistakes when implementing context routing, with examples and solutions.

## 1. The Monolith

**What it is:** Putting all information in a single 500+ line file.

**Example:**
```
CLAUDE.md (800 lines)
├── Agency identity (50 lines)
├── All detailed processes (200 lines)
├── Complete platform benchmarks (150 lines)
├── Info for all clients (200 lines)
├── Detailed compliance rules (100 lines)
└── Instructions for each skill (100 lines)
```

**Why it's a problem:**
- The agent loads EVERYTHING for any task, wasting tokens
- Hard to maintain — a change in benchmarks touches the same file as identity
- Higher probability the agent ignores parts due to context overload

**Solution:** Extract each concern to its own file. The router only points:
```
CLAUDE.md (40 lines) → points to:
├── agency/strategy/benchmarks.md
├── agency/processes/ad-policy-compliance.md
├── clients/[x]/CLAUDE.md
└── skills/[x]/SKILL.md
```

## 2. The Ghost

**What it is:** Referencing files or folders that don't exist.

**Example:**
```markdown
## Loading Rules
- For compliance: see agency/processes/ad-policy-compliance.md
```
But the file `ad-policy-compliance.md` was never created.

**Why it's a problem:**
- The agent tries to read a file that doesn't exist → silent error or confusion
- Gives a false sense of coverage ("we already have compliance documented")
- They accumulate over time if not audited

**Solution:**
- Periodically run the audit step 2 (link validation)
- Option A: Create the referenced file (even with minimal content + PENDING)
- Option B: Remove the reference until the file exists
- Never ignore — ghosts degrade trust in the system

## 3. Duplication

**What it is:** The same information appears in the router AND in the granular file.

**Example:**
```markdown
# Client router (CLAUDE.md)
Target CPL: <$12
Current CPL: ~$11.18
CRM: GoHighLevel
Sector: Financial education

# Context file (context/client-info.md)
Target CPL: <$12      ← duplicate
Current CPL: ~$11.18   ← duplicate
CRM: GoHighLevel        ← duplicate
Sector: Financial education
```

**Why it's a problem:**
- When data changes, it needs updating in 2+ places
- If only updated in one, the other becomes outdated → inconsistency
- The agent may read contradictory data

**Solution:**
- The router can have a "quick reference table" with key data — this is acceptable as a summary
- But the source of truth is ALWAYS the granular file
- If there's conflict, the granular file wins
- Limit duplication to the minimum needed for the router to be useful without opening other files

## 4. Total Load

**What it is:** Loading all context files for an entity for any task.

**Example:**
```markdown
## Context Files
Read ALL these files before any work:
1. context/client-info.md
2. context/offer.md
3. context/buyer-personas.md
4. context/funnel.md
5. context/accounts.md
6. context/brand-guidelines.md
7. context/competitors.md
8. admin/contract.md
9. admin/pricing.md
10. admin/invoicing.md
```

**Why it's a problem:**
- For a simple task ("change the ad headline"), loading 10 files is excessive
- Wastes tokens and context window
- Dilutes the agent's attention with irrelevant information

**Solution:** Separate into mandatory (base context) and conditional (per task):
```markdown
## Base Context (ALWAYS)
1. context/client-info.md
2. context/offer.md

## For Copywriting (ADDITIONAL)
3. context/buyer-personas.md
4. context/brand-guidelines.md

## For Admin (ADDITIONAL)
5. admin/contract.md
6. admin/pricing.md
```

## 5. The Router-that-doesn't-route

**What it is:** A file that describes content but doesn't say WHEN to read it.

**Example:**
```markdown
# Knowledge Base

## Contents
- strategy/ — Frameworks and methodology
- processes/ — SOPs and procedures
- benchmarks/ — Performance data
- admin/ — Billing and pricing
```

**Why it's a problem:**
- It's an index, not a router
- The agent doesn't know what to load for each type of task
- Ends up reading everything (Total Load) or nothing

**Solution:** Add conditional loading rules:
```markdown
## Loading Rules
1. For methodology: read strategy/ first
2. For "how we do X": read processes/
3. For compliance: read processes/ad-policy-compliance.md
4. For performance data: read benchmarks/
5. For billing: read admin/
```

## 6. Cross-Context

**What it is:** Information from one entity visible in another's context.

**Example:**
- A report for Client A mentions Client B's data as comparison
- A context file references metrics from another client
- An agent responds to a question about Client A using data loaded from Client B

**Why it's a problem:**
- Confidentiality violation — each client is an isolated silo
- Can lead to erroneous recommendations based on wrong context
- Legal issues in regulated sectors

**Solution:**
- Strict rule in global router: "Each client's data is CONFIDENTIAL — never cross information between clients"
- If comparisons are needed, use anonymous agency benchmarks (agency/benchmarks/), never another client's data
- Verify that skills don't store context between sessions of different clients

## 7. Coupled Skill

**What it is:** A skill that depends on files outside its folder to function.

**Example:**
```markdown
# SKILL.md for ad-reporting
## Step 3: Analysis
Read agency/benchmarks/meta-cpl-by-sector.md to compare CPLs
Read agency/strategy/attribution-model.md to adjust attribution
```

**Why it's a problem:**
- If you copy the skill to another workspace, it doesn't work (missing external files)
- If external files change, the skill breaks without warning
- It's not a self-contained capsule

**Solution:**
- Everything the skill needs to function must be in its own folder (SKILL.md + references/)
- Platform benchmarks go in references/meta-ads.md inside the skill
- Client context is INJECTED at runtime (the skill requests it, the system provides it), not referenced directly

## Summary

| Anti-pattern | Warning sign | Quick fix |
|-------------|-------------|-----------|
| The Monolith | File > 200 lines | Extract to granular files |
| The Ghost | Referenced file doesn't exist | Create file or remove reference |
| Duplication | Same data in 2+ files | Define single source of truth |
| Total Load | 10+ files in mandatory loading | Separate mandatory vs conditional |
| The Router-that-doesn't-route | No conditional rules | Add "for X, read Y" rules |
| Cross-Context | Entity A's data in Entity B's context | Isolate silos, use anonymous benchmarks |
| Coupled Skill | Skill references external files | Move to references/ inside the skill |
