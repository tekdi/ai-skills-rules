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
- Known Issues
- References

**Do not omit any of these areas** even if they must be marked as:

> Information Not Found In Repository

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

Create the full folder structure below. Every listed file must exist (use a stub with
"Information Not Found In Repository" when the repo has no relevant content).

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
│   ├── known-issues.md
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
* Known Issues
* References
```

Each TOC entry must link to the corresponding markdown file using relative paths.

`wiki-index.md` is the navigational hub; `README.md` is the executive entry point with a
brief service summary at the top.

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
- Ownership of folders (if discoverable from CODEOWNERS or docs; otherwise note "Not specified")
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

When information is missing, use this blockquote consistently:

> Information Not Found In Repository

Optionally add: what was searched and where a future contributor should look.

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

**operations/** — Extract from Dockerfile, docker-compose, K8s manifests, Terraform, CI
workflows, and monitoring configs.

**engineering/** — Document real test commands, lint commands, env var tables, and patterns
observed in the codebase.

**knowledge/** — Glossary from domain terms in code. Technical debt from TODO/FIXME/HACK
comments and known limitations in README/issues.

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
- [ ] Every BOT checklist section is represented (or marked not found)
- [ ] All five documentation layers are covered across the wiki
- [ ] No hallucinated APIs, tables, or config keys
- [ ] Cross-links work between related documents
- [ ] Mermaid diagrams render for major flows and components

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
| ...         | ✅ / ⚠️ Not found | link |

### Gaps Requiring Human Input
- <list sections marked "Information Not Found In Repository">

### Suggested Next Steps
- <review business rules, validate env var table, add architecture.png, etc.>
```

---

## Behavioral Rules

- **Analyze before writing.** Read source code; do not guess from folder names alone.
- **Code is truth.** Every technical claim must trace to a file in the repository.
- **Complete over perfect.** Create every required file; stub missing sections honestly.
- **Link aggressively.** Any mention of another topic should link to its wiki page.
- **Optimize for AI agents.** `ai-context.md` is the highest-priority deliverable.
- **Do not modify application source code** unless the user explicitly asks — this skill
  produces documentation only.
- **Incremental updates.** When regenerating, diff against existing wiki and preserve
  accurate content while refreshing stale sections.

The final output should be comprehensive enough that a new engineer, architect, DevOps
engineer, support engineer, or AI coding agent can independently understand, maintain,
troubleshoot, deploy, and enhance the service.
