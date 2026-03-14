# Anatomy of a Context File

A context file contains operational information about a single concern. It's what routers point to.

## Standard Frontmatter

Every context file must have this YAML frontmatter at the top:

```yaml
---
entity: acme-corp              # Slug of the entity it belongs to
domain: context                # Domain: context, admin, strategy, campaigns
concern: offer                 # Specific concern: offer, funnel, pricing, etc.
last-updated: 2026-03-13       # Date of last update
updated-by: victor             # Who last updated it
---
```

**What frontmatter is for:**
- **Freshness auditing:** Detect outdated files ("this file hasn't been updated in 60 days")
- **Automatic indexing:** In a web interface, allows searching and filtering by entity, domain, or concern
- **Traceability:** Know who made the last change and when
- **Integrity:** Verify the file belongs to the correct entity

## Internal Structure

### 1. Title (H1)
Descriptive name of the concern. E.g.: "Product / Offer", "Funnel Structure".

### 2. Quick reference table (optional)
For files with key data, a table at the top allows quick scanning without reading everything:

```markdown
| Field | Value |
|-------|-------|
| **Name** | Product XYZ |
| **Price** | $2,000 |
| **Type** | Group mentorship |
```

### 3. Sections with H2
Each aspect of the concern in its own section:

```markdown
## Main Product
[Product description]

## Unique Mechanism
[USP and differentiator]

## Core Promise
[What it promises the client]

## Current Campaign Offer
[Lead magnet, entry point, close]
```

### 4. Pending data
Clearly mark what's missing with `PENDING`:
```markdown
- PENDING: confirm updated prices for phase 2
- PENDING: receive dossier with villa models
```

## Full Example: offer.md

```markdown
---
entity: acme-saas
domain: context
concern: offer
last-updated: 2026-03-13
updated-by: victor
---

# Product / Offer

## Main Product

| Field | Value |
|-------|-------|
| **Name** | Acme Analytics — AI-powered B2B analytics |
| **Type** | SaaS platform |
| **Starting price** | $299/mo |
| **Enterprise** | Custom pricing |

## Unique Mechanism: Real-time Attribution

This is USP #1. Most analytics tools rely on last-click attribution.
Acme uses multi-touch attribution with AI weighting to show true
campaign impact across the full funnel.

## Core Promise
See exactly which campaigns drive revenue, not just clicks,
with attribution you can actually trust.

## Current Campaign Offer
- Lead magnet: Free attribution audit
- Close: Demo call with sales team

## Backend / Upsells
- Enterprise tier
- Custom integrations
- PENDING: confirm managed services pricing
```

## Size Guidelines

| Range | Status | Action |
|-------|--------|--------|
| < 50 lines | May be incomplete | Verify if information is missing |
| 50-200 lines | Optimal | Maintain |
| 200-300 lines | Long | Evaluate if it can be split |
| > 300 lines | Too long | Split into 2+ files by sub-concern |

**How to split:** If offer.md grows too large because the client has multiple products, create offer-product-a.md, offer-product-b.md and keep offer.md as an index that points to the sub-files.

## What does NOT go in a context file

- **Historical decisions** → go in decision-log.md (append-only)
- **Campaign changes** → go in change-log.md
- **Performance metrics** → go in reports
- **Workflow instructions** → go in skills (SKILL.md)
- **Loading rules** → go in routers (CLAUDE.md)
