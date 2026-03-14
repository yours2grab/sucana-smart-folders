# Routing Rules Catalog

Loading rules define WHEN each resource is read. They're the central piece of the framework — without conditional rules, a router is just an index.

## Type 1: Mandatory Loading (ALWAYS)

Executed before any work with the entity. No condition.

| Rule | Example |
|------|---------|
| Always read the active entity's router | "ALWAYS read the client's CLAUDE.md before doing any work" |
| Always read base context files | "Read context/client-info.md and context/offer.md before any task" |

**When to use:** For information relevant in 100% of tasks with that entity.

**Caution:** Don't overuse mandatory loading. If a file is only relevant for 30% of tasks, it should be conditional.

## Type 2: Conditional Loading by Task Type

Executed only when the task matches the condition.

| Condition | Resource to load | Example |
|-----------|-----------------|---------|
| Campaign analysis | Reporting skill + platform references | "For campaign analysis: load skills/ad-reporting/SKILL.md" |
| Copywriting | Copywriting skill | "For copywriting: load skills/copywriter/SKILL.md" |
| Billing/admin | Client admin files + agency admin | "For billing: read clients/[x]/admin/ + agency/admin/" |
| Strategy | Frameworks and benchmarks | "For strategy: read agency/strategy/" |

**Pattern:** `For [task type]: load [resource]`

## Type 3: Conditional Loading by Platform/Channel

Executed based on the platform or channel involved.

| Condition | Resource | Example |
|-----------|---------|---------|
| Meta Ads detected | Meta reference | "Meta Ads → read references/meta-ads.md" |
| Google Ads detected | Google reference | "Google Ads → read references/google-ads.md" |
| Multiple platforms | All relevant references | "Multiple platforms → read all relevant files" |

**Pattern:** `[Platform] → load [specific reference]`

## Type 4: On-Demand Loading (within workflow)

Executed at a specific step in a workflow, not at the start.

| Workflow step | Resource | Example |
|--------------|---------|---------|
| Step 3: Analysis | Platform benchmarks | "Read the relevant platform reference file(s) for metrics and benchmarks" |
| Step 5: HTML report | Report template | "Read references/report-template.md and generate two versions" |

**Pattern:** The resource is referenced WITHIN the workflow step, not in the router's loading rules.

**Advantage:** The agent doesn't load the report template until it actually needs it (step 5), instead of loading it at the start.

## Type 5: Priority Loading

Defines the order to consult sources when it's unclear what to load.

| Priority | Source | Example |
|----------|--------|---------|
| 1 (first) | Strategy | "strategy/ — methodology and frameworks" |
| 2 | Processes | "processes/ — how we do X" |
| 3 | Benchmarks | "benchmarks/ — performance data" |
| 4 | Tech stack | "tech-stack/ — tools" |
| 5 (last) | Admin | "admin/ — pricing, billing" |

**Pattern:** Ordered list of sources for resolving ambiguous queries.

**When to use:** When the domain has multiple subfolders and it's unclear which to consult first. Priority prevents the agent from reading everything.

## How to Write Good Rules

### YES: Specific and actionable rule
```
For campaign analysis: load skills/ad-reporting/SKILL.md
```

### NO: Vague rule
```
Read the relevant files as needed
```

### YES: Clear condition
```
For billing/admin questions: read clients/[client]/admin/ + agency/admin/
```

### NO: Ambiguous condition
```
If it's something about admin, look around there
```

## Composition Rules

When a task activates multiple rules, they compose like this:

1. **Mandatory loading** always executes first
2. **Conditional loading** is added based on task type
3. **Authority hierarchy** resolves conflicts (Entity > Domain > Global)
4. **On-demand loading** executes at the workflow step that needs it

Example: "Write copy for client Acme"
1. Mandatory: read clients/acme/CLAUDE.md
2. Conditional (copywriting): load skills/copywriter/SKILL.md
3. Conditional (context): load clients/acme/context/offer.md + buyer-personas.md
4. On-demand (step 3 of skill): read references/hooks-library.md
