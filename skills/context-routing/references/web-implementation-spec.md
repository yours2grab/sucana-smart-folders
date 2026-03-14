# Technical Specification — Web Implementation of Context Routing

This document defines how the context routing framework can be implemented as a web product for agencies and teams.

## 1. Data Model

### Main entities

```
Workspace (1 per organization)
├── id, name, slug, created_at
├── domains[] → Domain
└── settings (timezone, language, currency)

Domain (agency, clients, skills, tools)
├── id, workspace_id, name, slug, type
├── router → Router
└── entities[] → Entity

Entity (a client, a skill, a tool)
├── id, domain_id, name, slug, status, created_at
├── router → Router
└── context_files[] → ContextFile

Router (routing configuration)
├── id, owner_type (workspace|domain|entity), owner_id
├── identity_text (2-3 lines)
├── loading_rules[] → LoadingRule
└── restrictions[] (free text)

LoadingRule (loading rule)
├── id, router_id, type (mandatory|conditional|on_demand|priority)
├── condition (null for mandatory, text for conditional)
├── target_files[] → ContextFile IDs
└── priority_order (for type=priority)

ContextFile (context file)
├── id, entity_id, domain_slug, concern, content (markdown)
├── last_updated, updated_by
├── version (autoincrement on each change)
└── status (draft|active|stale|archived)

AuditLog (change record — append-only)
├── id, workspace_id, entity_id, context_file_id
├── action (create|update|delete|propose|approve|reject)
├── diff (what changed)
├── proposed_by (system|user_id)
├── approved_by (user_id, null if pending)
├── created_at
└── session_id (to group changes from the same session)
```

### Key relationships
- A Workspace has many Domains
- A Domain has many Entities
- Each Workspace, Domain, and Entity has exactly one Router
- A Router has many LoadingRules
- An Entity has many ContextFiles
- Each change to a ContextFile generates an AuditLog

## 2. Context Injection Flow

When a user starts a task, the system decides what context to send to the LLM:

### Step 1: Detect task type
The system classifies the user's task into a category:
- **reporting** — campaign analysis, CSVs
- **copywriting** — creating ads, hooks, bodies
- **admin** — billing, contracts, pricing
- **strategy** — planning, roadmap, decisions
- **onboarding** — setting up new client
- **general** — open questions

Detection can be:
- **Explicit:** the user selects the task type in the UI
- **Implicit:** a classifier LLM analyzes the user's message
- **By command:** the user invokes a specific skill (/weekly-report, /write-copy)

### Step 2: Resolve active entity
Identify what entity the user is working on:
- If there's a client selected in the UI → use that client
- If the message mentions a client → resolve by name/slug
- If no clear entity → ask

### Step 3: Load router and resolve rules
```
1. Load Workspace Router (global)
2. Load Domain Router (if applicable)
3. Load active Entity Router
4. Collect all LoadingRules:
   - type=mandatory → always include target_files
   - type=conditional → evaluate condition against task type
   - type=priority → order and take the first N relevant
5. Result: list of ContextFile IDs to inject
```

### Step 4: Build system prompt
```
System prompt =
  [Workspace identity (from global router)]
  + [Global rules (security, compliance)]
  + [Client context (router + selected context files)]
  + [Active skill (if applicable)]
  + [User message]
```

**Key principle:** The system prompt does NOT contain all ContextFiles for the client. Only those that the LoadingRules determine as necessary for this task.

## 3. "Living Entity" — Auto-update System

The key differentiator: context enriches organically with each interaction.

### Auto-update flow

```
1. User completes a work session
   (e.g., processes a meeting, analyzes campaigns, reviews strategy)

2. System analyzes the session and detects new information:
   - New client data (updated metrics, price changes)
   - New audience insights (objections, motivations)
   - Strategic decisions made
   - Changes to funnel or offer

3. System generates UPDATE PROPOSALS:
   "Based on this session, I propose updating:"
   - context/offer.md → Add new product line (lines X-Y)
   - context/buyer-personas.md → Update objections for profile 2
   - strategy/decision-log.md → Record decision to pause Google Ads

4. HUMAN LOOP: User reviews each proposal
   - [Approve] → change applied + recorded in AuditLog
   - [Reject] → discarded + rejection recorded in AuditLog
   - [Edit] → user modifies the proposal then approves

5. Updated ContextFiles are available for the next session
```

### Types of automatic update

| Trigger | What it detects | What it proposes to update |
|---------|----------------|--------------------------|
| After meeting | New data mentioned, decisions made | context/, strategy/decision-log.md |
| After report | Updated metrics, new KPIs | Client router (quick reference) |
| After copywriting | New hooks that work, refined tone | context/brand-guidelines.md |
| After onboarding | Progressively filled info | All context files |
| Periodically | Files not updated in >30 days | Review notification |

## 4. New Organization Onboarding

### Registration flow

```
1. Organization registers
   → Empty Workspace created

2. Initial setup wizard:
   a. Organization name, main services, markets
   b. Global Router generated with identity
   c. Base Domains created (agency, clients, skills)

3. First client:
   a. Guided wizard: name, slug, sector, main product
   b. Client template structure copied
   c. Information requested progressively:
      - Session 1: client-info + offer (minimum to start)
      - Session 2: buyer-personas + funnel (for campaigns)
      - Session 3: accounts + brand-guidelines (for production)
   d. Organization can upload existing docs → system extracts and proposes ContextFiles

4. System starts working with partial information
   → Audit shows "files pending completion"
   → With each session, updates are proposed (living entity)
```

### Import from existing systems
- If the organization already has a workspace in markdown files:
  - System can parse the folder structure
  - Map CLAUDE.md → Routers
  - Map .md files → ContextFiles with auto-detected frontmatter
  - User validates and adjusts the mapping

## 5. Automated Audit

### Workspace health dashboard

The system runs the audit-checklist automatically and presents:

```
┌─────────────────────────────────────────┐
│ Workspace Health         Score: 87/100  │
├─────────────────────────────────────────┤
│ ✅ Routers complete       12/12         │
│ ⚠️ Ghosts detected        2             │
│ ✅ Orphans                0             │
│ ⚠️ Missing frontmatter    5 files       │
│ 🔴 Stale (>30d)          3 files       │
│ ✅ Template consistency   100%          │
└─────────────────────────────────────────┘
```

### Soft notifications
- "It's been 30 days since you updated buyer-personas.md for Client X"
- "The file ad-policy-compliance.md is referenced but doesn't exist"
- "Client Y doesn't have a CLAUDE.md — context may be incomplete"

### Frequency
- **Daily:** Link validation (ghosts)
- **Weekly:** Context freshness + template consistency
- **Monthly:** Full health report with score

## 6. Permissions and Roles

### Roles
- **Owner:** Full workspace access. Can configure routers, approve changes, manage members.
- **Manager:** Access to assigned clients. Can approve auto-update proposals.
- **Analyst:** Read-only + can execute skills (reporting, copywriting). Cannot modify context.

### Isolation rules
- A Manager only sees their assigned clients (equivalent to "never cross information")
- Auto-update proposals are only visible to Owner and the client's Manager
- AuditLogs are visible to Owner and Manager

## 7. API Endpoints (reference)

```
# Workspaces
GET    /api/workspaces/:id
GET    /api/workspaces/:id/health          # Health dashboard

# Entities (clients)
GET    /api/entities/:id
GET    /api/entities/:id/context           # All ContextFiles
POST   /api/entities                       # Create from template

# Context Resolution (injection)
POST   /api/context/resolve                # Input: entity_id + task_type
                                           # Output: list of ContextFiles to inject

# Auto-update
GET    /api/proposals                      # Pending proposals
POST   /api/proposals/:id/approve
POST   /api/proposals/:id/reject

# Audit
GET    /api/audit/report                   # Latest health report
GET    /api/audit/ghosts                   # Detected ghosts
GET    /api/audit/stale                    # Stale files
```
