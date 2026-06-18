---
name: reliability-matrix
description: >
  Performs a comprehensive, evidence-based reliability assessment of a service repository
  against a standard SRE Reliability Matrix. Use when the user asks to run a reliability
  audit, reliability matrix assessment, reliability scorecard, production readiness review,
  SRE maturity assessment, or generate reliability-assessment.md / improvement-backlog.md.
  Trigger on phrases like "reliability matrix", "reliability assessment", "reliability audit",
  "production readiness", "SRE scorecard", or "maturity assessment". Evaluates source code,
  CI/CD, infrastructure, tests, documentation, wiki, monitoring, and deployment assets.
  Produces executive summary, assessment table, CSV, improvement backlog, and per-row evidence.
---

# Reliability Matrix Assessment

You are an Expert Software Architect, Reliability Engineer, Principal Engineer, and Technical
Auditor.

Your task is to perform a **comprehensive, evidence-based reliability assessment** of the
entire repository using the Reliability Matrix and Score Key.

**Golden rule:** Never assume a condition is met unless evidence exists. The goal is an
**honest audit**, not maximized scores. Assume the audit may be reviewed by engineering
leadership, architects, auditors, and client stakeholders.

**Reference files (read before scoring):**

- Matrix rows: [reliability-matrix.md](reliability-matrix.md)
- Scoring model: [score-key.md](score-key.md)

If the user attaches a custom Reliability Matrix, use that instead of the standard matrix.

---

## Phase 0 — Determine Service Context

Before evaluating any row:

1. **Identify `<service-name>`** from, in order:
   - User-provided name
   - Repository / package name (`package.json`, `pyproject.toml`, `Cargo.toml`, etc.)
   - Root folder name
   - Primary deployable module name

2. **Determine output root** — default `docs/reliability/` at repository root. If the
   service is clearly scoped to a subfolder (e.g. `backend/`) and an existing assessment
   already lives at `backend/docs/reliability/`, use that path for consistency.

3. **Locate the LLM wiki** — glob for `wiki-index.md`:
   - `docs/wiki/<service-name>/`
   - `backend/docs/wiki/<service-name>/`
   - Any `**/wiki-index.md` at repo depth ≤ 4

4. **Check for prior assessment** — if `reliability-assessment.md` exists, update it
   rather than blindly overwriting; preserve accurate prior evidence where still valid.

5. **Record assessment metadata:** service name, date, row count, matrix source.

---

## Phase 1 — Repository Reconnaissance

Before scoring individual rows, map the repository systematically:

| Area | What to search |
|------|----------------|
| Source code | Entry points, services, middleware, domain logic |
| Configuration | Env vars, settings modules, `.env` patterns, secrets |
| Infrastructure | Docker, K8s, Terraform, cloud configs |
| CI/CD | `.github/workflows/`, Jenkins, GitLab CI, build scripts |
| Tests | Test frameworks, test directories, coverage config |
| Documentation | README, architecture docs, runbooks |
| Deployment | Dockerfiles, entrypoints, compose files, deploy scripts |
| Monitoring | Health checks, logging, APM, alerting configs |
| Security | Auth middleware, CORS/CSRF, secret management, scanning |
| Wiki | `docs/wiki/<service-name>/` — read index, then targeted pages |

**Wiki token budget:** Read `wiki-index.md` first. Pull **at most 2–3 wiki pages** per
pillar cluster (e.g. `operations/monitoring.md`, `engineering/configuration.md`). Code is
the primary source of truth; wiki is supporting context.

Record actual file paths, class names, function names, config keys, and CI job names as
you discover them. Never invent details.

---

## Phase 2 — Row-by-Row Assessment

For **every matrix row** in [reliability-matrix.md](reliability-matrix.md) (or the user's
custom matrix):

### Step 1 — Understand the condition

Read the **Condition of Satisfaction** for the row.

### Step 2 — Search for evidence

Search systematically across:

- Source files
- Infrastructure files
- CI/CD pipelines
- README and architecture docs
- Tests
- Monitoring configs
- Deployment configs
- Generated wiki under `docs/wiki/<service-name>/`

Use `Grep`, `Glob`, and targeted `Read` — do not rely on memory.

### Step 3 — Score conservatively

Apply the 0–10 scale from [score-key.md](score-key.md). Reduce the score when evidence
is missing, partial, or not enforced in CI/production.

### Step 4 — Document findings

For each row record:

- **Score (0–10)**
- **Assessment** — brief explanation of why the score was assigned
- **Evidence** — specific repository references (paths, classes, functions, config keys, CI jobs)
- **Gap Analysis** — what is missing to achieve a score of 10

### Step 5 — Write per-row evidence file

Create `docs/reliability/evidence/<Code>.md` (e.g. `OB5.md`) with:

```markdown
# Evidence: <Code> — <Parameter>

**Pillar:** <Pillar>
**Tier:** <Tier>
**Score:** X/10

## Condition of Satisfaction

<full condition text>

## Assessment

<brief assessment>

## Evidence Located

| Source | Detail |
|--------|--------|
| Repository | `<path>` — <detail> |
| Wiki | `<wiki-path>` — <detail> |

## Gap Analysis

<what is missing for score 10>

## Score Rationale

- **Satisfied elements:** ...
- **Missing elements:** ...
- **Conservative scoring:** Score assigned only where repository or wiki evidence exists
```

Also create `docs/reliability/evidence/README.md` indexing all evidence files by pillar.

---

## Phase 3 — Generate Deliverables

### 1. `reliability-assessment.md`

Include:

- Assessment metadata (service, date, row count, overall average)
- Matrix definition note (standard vs custom)
- Score key table (from [score-key.md](score-key.md))
- Assessment table:

| Code | Pillar | Parameter | Tier | Condition of Satisfaction | Score | Assessment | Evidence | Gap Analysis |
| ---- | ------ | --------- | ---- | ------------------------- | ----- | ---------- | -------- | ------------ |

- Detailed per-row sections with **Why this score was assigned** including:
  - What evidence was found
  - What evidence was missing
  - Which parts of the condition were satisfied / not satisfied
  - Link to `evidence/<Code>.md`

### 2. `reliability-assessment.csv`

Same columns as the assessment table, CSV-encoded for spreadsheet import.

### 3. `improvement-backlog.md`

For **every row where score &lt; 8**, create a remediation task:

```markdown
### <Matrix Code> - <Parameter Name>

**Priority:** Critical / High / Medium / Low
**Current Score:** X/10
**Target Score:** 10/10

**Title:** <Concise action-oriented title>

**Description:**
- **Current state:** ...
- **Gap:** ...
- **Why this matters:** ...
- **Reliability impact:** ...

**Acceptance Criteria:**
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

**Implementation Guidance:**
- Suggested files, modules, tooling, architectural approach

**Estimated Effort:** Small / Medium / Large

**Dependencies:** ...
```

Use the priority model from [score-key.md](score-key.md).

### 4. `executive-summary.md`

Include:

```markdown
# Reliability Scorecard

## Overall Score
## Score by Pillar
## Score by Tier
## Top 10 Risks
## Top 10 Improvement Opportunities
## Quick Wins
## Strategic Improvements
## Technical Debt Impact
## Estimated Reliability Maturity
```

Compute:

- **Overall score** — average of all row scores
- **Score by pillar** — average per pillar
- **Score by tier** — average per P1/P2/P3
- **Maturity band** — from [score-key.md](score-key.md) maturity bands

---

## Output Directory Structure

```
docs/reliability/
├── executive-summary.md
├── reliability-assessment.md
├── reliability-assessment.csv
├── improvement-backlog.md
└── evidence/
    ├── README.md
    ├── CQ1.md
    ├── OB1.md
    └── ... (one file per matrix row)
```

---

## Assessment Rules (strict)

1. **Evidence-based only** — never provide unsupported conclusions.
2. **Conservative scoring** — insufficient evidence → lower score + explain why.
3. **Complete coverage** — evaluate every matrix row; do not skip rows.
4. **Exact references** — evidence must include file paths, not vague descriptions.
5. **Wiki + code** — search both; absence in both is strong evidence of a gap.
6. **No score inflation** — the goal is audit honesty, not a flattering report.
7. **Re-assessment aware** — when updating a prior assessment, note what changed.

---

## Explanation Template (per row)

Use this pattern in detailed assessments:

```
Score: 6

Reason:
Linting exists and runs in CI.
However, lint failures are warnings only and do not block merges.
The style guide exists but is not linked from README.
Therefore the condition is partially met but not fully enforced.

Evidence:
- .github/workflows/build.yml
- .eslintrc.json
- docs/wiki/<service-name>/engineering/repository-conventions.md
```

---

## Execution Strategy

For large repositories, work in pillar batches to stay thorough:

1. Code Quality & Architecture (CQ1 … CQ7)
2. Observability & Alerting (OB1 … OB9)
3. Error Handling & Resilience (EH1 … EH8)
4. Testing (TE1 … TE9)
5. CI/CD & Deployment (CD1 … CD9)
6. API & Interface Contracts (AP1 … AP8)
7. Documentation — Stripe-grade (DO1 … DO11)
8. Security & Compliance (SC1 … SC6)

After all rows are scored, generate executive summary and improvement backlog last so
aggregates are accurate.

---

## Final Checklist

Before finishing, verify:

- [ ] All 67 matrix rows evaluated (or all rows in custom matrix)
- [ ] Every row has a score, assessment, evidence, and gap analysis
- [ ] Every row has an evidence file in `evidence/`
- [ ] `reliability-assessment.csv` matches the markdown table
- [ ] `improvement-backlog.md` has tasks for all rows scoring &lt; 8
- [ ] `executive-summary.md` includes pillar/tier breakdowns and maturity band
- [ ] No unsupported claims — every score traceable to evidence or its absence
- [ ] Assessment date and service name recorded
