# Scaling Playbook

How to add new clients, skills, and domains to the workspace without breaking the structure.

## Operation 1: New Client

### Checklist

- [ ] Copy `clients/_template/` to `clients/[new-slug]/`
- [ ] Fill `CLAUDE.md` for the new client (basic data, account IDs, KPIs)
- [ ] Fill `context/client-info.md` (general data, contacts, stack)
- [ ] Fill `context/offer.md` (product, price, USP)
- [ ] Fill `admin/contract.md` (contract type, dates, parties)
- [ ] Fill `admin/pricing.md` (pricing structure, tax data)
- [ ] Fill `admin/services.md` (contracted services)
- [ ] Add standard frontmatter to all context files
- [ ] Register the new client in the global router ("Active Clients" section)
- [ ] Complete `admin/onboarding.md` checklist

### Recommended fill order
1. **First:** CLAUDE.md + admin/ (contract, pricing, services) — needed to operate
2. **Second:** context/client-info.md + context/offer.md — base for any work
3. **Third:** context/buyer-personas.md + context/funnel.md — needed for campaigns
4. **Fourth:** The rest of context/ as needed

### Important note
You don't need to fill EVERYTHING at once. Files can start with placeholders and be completed progressively. The system is designed to work with partial information — the audit will detect which files are empty so you can fill them over time.

## Operation 2: New Skill

### Checklist

- [ ] Create folder `skills/[skill-name]/`
- [ ] Create `SKILL.md` with frontmatter (name, description) + complete workflow
- [ ] Create `references/` with support files the skill needs
- [ ] Verify the skill is self-contained: works with just SKILL.md + references/
- [ ] Register in `skills/CLAUDE.md` (list of available skills)
- [ ] Add conditional loading rule in global router: "For [task]: load skills/[name]/SKILL.md"
- [ ] (Optional) Create custom command that orchestrates skill + context loading

### Minimum skill structure
```
skills/new-skill/
├── SKILL.md              # Definition + workflow + hard rules
└── references/
    ├── reference-1.md   # Support material
    └── reference-2.md   # Benchmarks, templates, etc.
```

### SKILL.md pattern
```yaml
---
name: skill-name
description: "Clear description of what it does and when to invoke it"
---

# Skill Title

[Agent role when executing this skill]

## Workflow — N Steps
[Sequential steps with explicit human loops]

## Hard Rules
[Non-negotiable rules]
```

## Operation 3: New Domain

### Checklist

- [ ] Create root folder: `[new-domain]/`
- [ ] Create router: `[new-domain]/CLAUDE.md`
- [ ] Define domain subfolders
- [ ] Register in global router (workspace structure table)
- [ ] Add conditional loading rule in global router
- [ ] (If applicable) Create `_template/` inside the domain for repeatable entities

### When to create a new domain
- When a category of information appears that doesn't fit in existing domains
- When an existing domain grows too large and has clearly differentiated sub-areas
- Examples: `partners/` (if the agency works with partners), `training/` (training material)

### Minimum domain structure
```
new-domain/
├── CLAUDE.md           # Domain router
├── [subfolder-1]/      # First area
└── [subfolder-2]/      # Second area
```

## Post-Scaling Verification

After any scaling operation, run:

1. **Audit step 2** (link validation) — verify no new ghosts
2. **Review global router** — confirm the new entity/skill/domain is registered
3. **Load test** — simulate a task with the new entity to verify routing works
