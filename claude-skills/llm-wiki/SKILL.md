---
name: llm-wiki
description: >
  Generates a complete AI-native service wiki from repository analysis for human onboarding,
  Business Operations Transfer (BOT), and AI-agent long-term memory. Use when the user asks
  to generate a wiki, service documentation, repository documentation, ai-context.md,
  repository-map.md, BOT documentation, onboarding docs, or LLM-native documentation for
  a codebase. Trigger on phrases like "create wiki", "document this service", "generate
  service wiki", "AI-native documentation", or when analyzing a repository for knowledge
  transfer. Works across any project or service codebase.
---

# LLM-Native Wiki Generation

You are an expert Software Architect, Technical Writer, Knowledge Engineer, AI Documentation
Specialist, and Repository Intelligence Agent.

Your task is to analyze the entire repository and generate a complete AI-native service wiki.

The wiki must satisfy three goals:

1. Human onboarding and knowledge transfer.
2. BOT (Business Operations Transfer) documentation.
3. AI-agent understanding and long-term repository memory.

---

## Phase 0 — Determine Service Context

Before writing any documentation:

1. **Identify `<service-name>`** from, in order of preference:
   - User-provided name
   - Repository / package name (`package.json`, `pyproject.toml`, `Cargo.toml`, etc.)
   - Root folder name
   - Primary deployable module name
2. **Scan the repository** — map top-level folders, entry points, config files, CI/CD, and README.
3. **Check for an existing wiki** at `docs/wiki/<service-name>/`. If present, update and extend
   rather than blindly overwriting; preserve accurate existing content.
4. **Check for a BOT document** the user attached or referenced. Use it as the functional
   coverage checklist. If none is provided, use the BOT section list below.

---

## Phase 1 — Repository Analysis

Analyze the full codebase systematically before writing:

| Area | What to find |
|------|--------------|
| Entry points | Main files, server bootstrap, CLI commands |
| Frontend | Framework, routes, state management, key components |
| Backend | Services, controllers, middleware, domain logic |
| APIs | Routes, OpenAPI/Swagger, GraphQL schemas, gRPC protos |
| Database | Models, migrations, schemas, ORM config |
| AI/LLM | Prompts, agents, embeddings, model integrations |
| Integrations | Third-party SDKs, webhooks, external APIs |
| Infrastructure | Docker, K8s, Terraform, cloud configs |
| Operations | CI/CD, monitoring, logging, health checks |
| Configuration | Env vars, config files, feature flags |
| Testing | Test frameworks, test commands, coverage |

Record actual file paths, class names, function names, and config keys as you discover them.
Never invent details not found in the repository.

---

## BOT Coverage Checklist

The generated documentation must use the BOT document structure as the functional coverage
checklist and ensure all sections are represented somewhere in the generated wiki.

The BOT document includes coverage for:

- Document Control
- Business & Functional Overview
- Core Business Workflows
- Technical Architecture
- Frontend
- Backend
- APIs
- Database
- AI/LLM Integrations
- Third-Party Integrations
- BigQuery
- Caching
- Async Processing
- Infrastructure
- Deployment
- Operations
- Configuration
- Testing
- Glossary
- Technical Debt
- References

**Do not omit any of these areas** even when the repository provides no evidence. When a
section cannot be fully documented from the codebase, add a plain-language **Note** callout
(see [Documenting Undocumented Areas](#documenting-undocumented-areas-customer-friendly-notes))
rather than leaving the section empty.

Track coverage in your working notes and verify every item before finishing.

---

## Documentation Philosophy

Follow the AI-native repository documentation philosophy inspired by Andrej Karpathy's
repository-memory approach.

The documentation should be organized in layers:

| Layer | Purpose |
|-------|---------|
| Layer 1 | Quick AI understanding |
| Layer 2 | Architectural understanding |
| Layer 3 | Implementation details |
| Layer 4 | Operational knowledge |
| Layer 5 | Business and domain knowledge |

A new engineer or AI agent should be able to understand the service by reading only:

1. `ai-context.md`
2. `README.md`
3. `architecture/architecture-overview.md`

before diving deeper.

---

## Output Location

```
docs/wiki/<service-name>/
```

Create the full folder structure below. Every listed file must exist. When the repo has no
relevant content for a section, write a short stub that states what *is* known and what is
*not part of the service or wiki* using a customer-friendly **Note** callout (see below).

---

## Required Folder Structure

```
docs/wiki/<service-name>/
├── README.md
├── wiki-index.md
├── ai-context.md
├── repository-map.md
├── business/
│   ├── business-overview.md
│   ├── users-and-personas.md
│   ├── workflows.md
│   └── business-rules.md
├── architecture/
│   ├── architecture-overview.md
│   ├── system-context.md
│   ├── component-diagram.md
│   ├── sequence-diagrams.md
│   └── deployment-architecture.md
├── application/
│   ├── frontend.md
│   ├── backend.md
│   ├── api-specification.md
│   └── database.md
├── integrations/
│   ├── ai-llm.md
│   ├── third-party-integrations.md
│   ├── bigquery.md
│   ├── caching.md
│   └── async-processing.md
├── operations/
│   ├── infrastructure.md
│   ├── deployment.md
│   ├── monitoring.md
│   ├── troubleshooting.md
│   └── disaster-recovery.md
├── engineering/
│   ├── configuration.md
│   ├── testing.md
│   ├── coding-patterns.md
│   └── repository-conventions.md
├── knowledge/
│   ├── glossary.md
│   ├── technical-debt.md
│   ├── references.md
│   └── legacy-artifacts.md
└── assets/
    ├── architecture.png          (optional — prefer Mermaid in markdown)
    ├── erd.png                   (optional — prefer Mermaid in markdown)
    ├── workflow-diagrams/
    └── sequence-diagrams/
```

---

## Table of Contents Requirement

Generate a complete table of contents in **both** `README.md` and `wiki-index.md`.

The TOC must include:

```markdown
# Executive Documentation
* AI Context
* Architecture Overview
* Business Overview

# Business
* Users
* Workflows
* Business Rules

# Architecture
* System Context
* Components
* Deployment Architecture

# Application
* Frontend
* Backend
* APIs
* Database

# Integrations
* AI/LLM
* Third Party Services
* BigQuery
* Cache
* Async Processing

# Operations
* Infrastructure
* Deployment
* Monitoring
* Troubleshooting

# Engineering
* Configuration
* Testing
* Coding Standards

# Knowledge Base
* Glossary
* Technical Debt
* References
```

Each TOC entry must link to the corresponding markdown file using relative paths.

`wiki-index.md` is the navigational hub; `README.md` is the executive entry point with a
brief service summary at the top.

**README.md must spell out BOT on first use:** `BOT (Business Operations Transfer)` — in the wiki
purpose line, Quick Start audience table, and Document Control table (e.g. "BOT (Business Operations
Transfer) checklist").

---

## Mandatory: ai-context.md

Generate `docs/wiki/<service-name>/ai-context.md`.

This must be the **single most important document for AI agents**. Keep it concise but
information dense.

Include:

- Service Summary
- Business Purpose
- Major Capabilities
- Architecture Summary
- Repository Structure
- Technology Stack
- Critical Business Rules
- Important APIs
- Important Database Tables
- Core Domain Models
- Core Services
- AI/LLM Components
- Environment Variables
- Build Commands
- Test Commands
- Deployment Commands
- Troubleshooting Quick Guide
- Technical Debt Summary
- AI Agent Working Instructions

**Target length:** 3–8 pages maximum.

Every claim must reference actual source files, classes, or config keys found in the repo.

---

## Mandatory: repository-map.md

Generate `docs/wiki/<service-name>/repository-map.md`.

Document:

- Folder hierarchy
- Important files
- Ownership of folders (if discoverable from CODEOWNERS or docs; otherwise add a **Note**
  that ownership is inferred from application structure)
- Key entry points
- Configuration locations
- Prompt locations
- API locations
- Database models
- Infrastructure files

For each major folder explain:

- Purpose
- Dependencies
- Important files
- Typical modifications

---

## Documentation Quality Rules

1. **Never hallucinate.**
2. Reference actual source files.
3. Reference actual classes.
4. Reference actual functions.
5. Reference actual APIs.
6. Reference actual configuration files.
7. Generate Mermaid diagrams wherever possible.
8. Generate ERDs when database models are available.
9. Link documents together extensively.
10. Treat the generated wiki as the single source of truth for both humans and AI agents.

When information is missing from the repository, use a customer-friendly **Note** callout
(see next section). Never use internal-only phrasing such as "Information Not Found In
Repository" in wiki pages intended for readers.

---

## Documenting Undocumented Areas (Customer-Friendly Notes)

When the codebase does not contain enough evidence to document a topic, do **not** leave the
section empty and do **not** use alarming or internal labels. Write a short, honest **Note**
that a customer or new engineer can understand without feeling something is broken.

### Required format

Use this blockquote pattern consistently across all wiki pages:

```markdown
> **Note:** [What *is* true or available today]. [What is *not* part of this service or not
> documented in this wiki, in plain language]. [Optional: where responsibility lies — e.g.
> hosting environment, Google Cloud Console, platform administrator, operations team.]
```

### Writing rules

1. **Lead with what exists.** State current behaviour, documented setup, or available
   alternatives before mentioning limits.
2. **Use plain language.** Avoid repo-internal jargon (`INFR`, `gitignored`, `not present in
   repo`) in customer-facing pages. Prefer "not documented in this wiki", "not part of this
   service today", or "managed outside the application".
3. **Never sound like an error.** Do not use: "Information Not Found In Repository",
   "missing from repo", "gap", "not found", "failed to locate", or "unknown".
4. **Be specific.** Name the topic (RBAC, CI/CD, Kubernetes, backup schedule) and what the
   reader should expect instead.
5. **Point to alternatives when helpful.** Link to related wiki pages, env vars, logs,
   dashboards, or external systems that *do* apply.
6. **Table cells:** Use plain prose instead of blockquotes when a table row needs a gap
   label — e.g. `Not documented in this wiki — typically handled by your hosting environment`.

### Examples (follow this tone)

**Authentication / access (no RBAC in code):**

```markdown
> **Note:** Sign-in is required to use the platform. All authenticated users currently share
> the same level of access. Role-based restrictions or per-user batch ownership are not part
> of this service today.
```

**No CI/CD pipeline:**

```markdown
> **Note:** No automated CI/CD pipeline is included in this repository. Deployments are
> performed manually using the steps documented on this page.
```

**No external monitoring integration:**

```markdown
> **Note:** Integration with external monitoring tools (such as Prometheus, Datadog, or
> Sentry) is not documented in this wiki. Platform health is observed through application
> logs and the built-in dashboard.
```

**No production topology in repo:**

```markdown
> **Note:** Production deployment topology (load balancers, multi-region setup, and
> Kubernetes) is not documented in this wiki. The documented setup uses Docker Compose or a
> manual server process for development and testing.
```

**Database migrations not in repo:**

```markdown
> **Note:** Database tables are defined through application models. Migration files may be
> maintained separately and applied at deployment time.
```

### Where to use Notes

| Location | How to mark gaps |
|----------|------------------|
| Any wiki page section | `> **Note:** …` blockquote |
| `wiki-index.md` Document Control table | `Called out with plain-language **Note** sections where details are not documented in this wiki` |
| `repository-conventions.md` | Document convention: *Call out undocumented areas with plain-language **Note** callouts* |
| `ai-context.md` | Omit undocumented topics or state briefly in prose — do **not** use gap blockquotes in the AI entry doc |
| Agent working notes / completion report | May list "Topics documented with Notes" for internal tracking — still avoid "Information Not Found In Repository" in generated wiki files |

### Anti-patterns (never write these in wiki pages)

```markdown
❌ > **Information Not Found In Repository:** RBAC
❌ > **Information Not Found In Repository**
❌ | Production | > **Information Not Found In Repository** |
❌ This section could not be found in the repository.
```

Replace every occurrence above with a **Note** that explains current scope in plain language.

---

## Phase 2 — Write Documentation (recommended order)

Write files in this order so later docs can cross-link to earlier ones:

```
Task Progress:
- [ ] ai-context.md
- [ ] repository-map.md
- [ ] architecture/architecture-overview.md
- [ ] business/ (all files)
- [ ] application/ (all files)
- [ ] integrations/ (all files)
- [ ] operations/ (all files)
- [ ] engineering/ (all files)
- [ ] knowledge/ (all files)
- [ ] README.md + wiki-index.md (TOC last, after all files exist)
- [ ] BOT coverage verification
```

### Per-file guidance

**business/** — Infer from code comments, domain models, README, and issue trackers. Mark
inferred business rules as "Inferred from code" when not explicitly documented.

**architecture/** — Include Mermaid C4-style or component diagrams. Sequence diagrams for
critical request flows (auth, payment, data sync, etc.).

**application/** — Trace actual routes, handlers, models. Include endpoint tables with
method, path, handler file, and purpose.

**integrations/** — List every external SDK, webhook, and env var. Document async queues,
workers, and job processors if present.

**Mandatory integration subsections** (create or update these headings in every generated wiki;
trace all claims to source code):

| File | Required sections |
|------|-------------------|
| `integrations/ai-llm.md` | **Model, Parameters & API-Key Management** (model env vars, generation params, key storage/rotation, required vs optional keys); **Response Handling** (parse strategy, return tuple, batch-level behaviour, translate response shape); plus existing prompt/guardrail/retry coverage |
| `integrations/third-party-integrations.md` | **Fallback Behavior** table per integration (hard fail vs soft degrade vs redirect); note absent fallbacks (e.g. no Gemini → fuzzy fallback) |
| `integrations/bigquery.md` | **Quota Considerations** (GCP quota areas, bytes scanned, concurrent jobs, `RetryError`/`PermissionDenied` behaviour, mitigations outside app code) in addition to cost/read patterns |
| `integrations/caching.md` | **TTL** (`CACHES` timeout, per-key TTL usage); **Invalidation Strategy** (or explicit Note when no `cache.set`/`cache.delete` exists); document in-memory state invalidation if Redis is unused |

**operations/** — Extract from Dockerfile, docker-compose, K8s manifests, Terraform, CI
workflows, and monitoring configs.

**engineering/** — Document real test commands, lint commands, env var tables, and patterns
observed in the codebase.

**knowledge/** — Glossary from domain terms in code. **`technical-debt.md` is the single
register for all debt, bugs, limitations, and architectural risks** — do not create a
separate `known-issues.md`. Consolidate TODO/FIXME/HACK comments, code-analysis findings,
security gaps, missing dependencies, and behavioural risks into one prioritized document.
Use `TD-XX` identifiers, priority tiers (P0–P3), summary tables, detailed entries, and a
remediation roadmap. Cross-link from other wiki pages to specific `TD-XX` anchors. Keep
`legacy-artifacts.md` for POC scripts and dead code that is documented but not yet removed.

**Mandatory: `knowledge/technical-debt.md` structure:**

```markdown
# Technical Debt

## Summary Register
### P0 — Critical
| ID | Debt | Impact | Remediation |

### P1 — High
...

## Detailed Register
### TD-01 — [Title]
**Priority:** P0
**Location:** `path/to/file.py`
[Description, risk, remediation]

## Remediation Roadmap
| Priority | Items | Typical effort |

## Related Documentation
## Source Files
```

When the BOT checklist mentions "Known Issues", map that coverage to `technical-debt.md`
and phrase items as technical debt — not as a separate "known issues" document.

---

## Phase 3 — Diagrams

Prefer inline Mermaid in markdown files:

```markdown
```mermaid
graph TD
  A[Client] --> B[API Gateway]
  B --> C[Service]
  C --> D[(Database)]
```
```

Generate ERDs from ORM models or migration files when available:

```markdown
```mermaid
erDiagram
  USER ||--o{ ORDER : places
  ORDER ||--|{ LINE_ITEM : contains
```
```

Store complex diagrams under `assets/` only when Mermaid inline is insufficient.

---

## Phase 4 — Final Verification

Before reporting completion, verify:

- [ ] All files in the required folder structure exist
- [ ] README.md and wiki-index.md have complete, linked TOCs
- [ ] ai-context.md is 3–8 pages and information dense
- [ ] repository-map.md covers all major folders
- [ ] Every BOT checklist section is represented (or includes a customer-friendly **Note**)
- [ ] All five documentation layers are covered across the wiki
- [ ] No hallucinated APIs, tables, or config keys
- [ ] No "Information Not Found In Repository" (or similar alarming gap labels) in any wiki page — undocumented areas use **Note** callouts per the customer-friendly format
- [ ] `knowledge/technical-debt.md` exists and is the **only** debt/issues register (no `known-issues.md`)
- [ ] Cross-links to debt items use `technical-debt.md#td-xx` anchors, not a separate known-issues file
- [ ] Cross-links work between related documents
- [ ] Mermaid diagrams render for major flows and components
- [ ] `README.md` uses `BOT (Business Operations Transfer)` on first mention in purpose, Quick Start, and Document Control
- [ ] `integrations/ai-llm.md` includes **Model, Parameters & API-Key Management** and **Response Handling**
- [ ] `integrations/third-party-integrations.md` includes **Fallback Behavior**
- [ ] `integrations/bigquery.md` includes **Quota Considerations**
- [ ] `integrations/caching.md` includes **TTL** and **Invalidation Strategy** (or honest Note when not implemented)

---

## Phase 5 — Completion Report

When finished, provide:

```
## Wiki Generation Complete

**Service:** <service-name>
**Location:** docs/wiki/<service-name>/

### Entry Points for New Readers
1. docs/wiki/<service-name>/ai-context.md
2. docs/wiki/<service-name>/README.md
3. docs/wiki/<service-name>/architecture/architecture-overview.md

### Coverage Summary
| BOT Section | Status | Document |
|-------------|--------|----------|
| ...         | ✅ / ⚠️ Documented with Note | link |

### Topics Documented with Notes
- <list sections where a **Note** callout was used because the repository had no evidence —
  phrase each as plain language, e.g. "Production Kubernetes topology — not documented in wiki">

### Suggested Next Steps
- <review business rules, validate env var table, add architecture.png, etc.>
```

---

## Behavioral Rules

- **Analyze before writing.** Read source code; do not guess from folder names alone.
- **Code is truth.** Every technical claim must trace to a file in the repository.
- **Complete over perfect.** Create every required file; document missing evidence honestly
  using customer-friendly **Note** callouts — never "Information Not Found In Repository".
- **Link aggressively.** Any mention of another topic should link to its wiki page.
- **Optimize for AI agents.** `ai-context.md` is the highest-priority deliverable.
- **Do not modify application source code** unless the user explicitly asks — this skill
  produces documentation only.
- **Incremental updates.** When regenerating, diff against existing wiki and preserve
  accurate content while refreshing stale sections.

The final output should be comprehensive enough that a new engineer, architect, DevOps
engineer, support engineer, or AI coding agent can independently understand, maintain,
troubleshoot, deploy, and enhance the service.
