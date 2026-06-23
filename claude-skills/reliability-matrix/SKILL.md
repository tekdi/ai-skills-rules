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
  Produces executive summary, assessment table, CSV, and improvement backlog.
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

### Step 4 — Document findings (detailed columns required)

For each row record **Score (0–10)** plus three **detailed** narrative columns.
These columns must be substantive enough for an auditor to verify the score without
reading source code. One-line summaries are **not acceptable**.

#### Assessment column (minimum 3–5 sentences)

Must answer all of:

1. Which parts of the **Condition of Satisfaction** are fully met, partially met, or not met
2. Why this **specific score** was chosen (and why not one point higher or lower)
3. Whether the capability is **ad hoc**, **documented**, **enforced in CI**, or **operationalized in production**
4. Any **conservative scoring** rationale when evidence is ambiguous

**Template:**

```
The condition requires [X]. [Component A] is present in [location] but [limitation].
[Component B] is absent — searched [paths/patterns]. Score N reflects partial
implementation: [satisfied elements] are in place, but [missing elements] prevent
a higher score because [reliability impact].
```

#### Evidence column (structured, path-specific)

Must include **both** positive and negative findings:

```
FOUND:
- `<path>` (line/class/function) — <what it proves>
- `<wiki-path>` — <supporting detail>

SEARCHED, NOT FOUND:
- `<pattern or path>` — <what was expected>
- CI jobs: <workflow names searched>
```

Rules:

- Every **FOUND** item must include a repository-relative file path
- Include **line numbers, class names, function names, or config keys** where applicable
- List **searches performed** when evidence is absent (proves the gap was investigated)
- Never write vague evidence like "logging exists" — specify the formatter, handler, and file

#### Gap Analysis column (actionable remediation)

Must include **concrete steps** to reach score 10:

```
TO REACH 10:
1. [Specific action] — target: `<file/module>` or CI job name
2. [Specific action] — tooling: e.g. GitHub Actions, Dependabot, OpenTelemetry
3. [Verification step] — how to confirm the gap is closed

BLOCKERS: [dependencies on other rows, infra, or team decisions]
EFFORT: Small / Medium / Large
```

Do not write generic gaps like "improve testing" — name the test type, directory, and threshold.

#### Column length guidance

| Column | Markdown (per-row sections) | CSV export |
|--------|----------------------------|------------|
| Assessment | 3–5 sentences | Same text, CSV-escaped |
| Evidence | FOUND + SEARCHED sections | Same text, semicolon-separated lists |
| Gap Analysis | Numbered steps + effort | Same text |

The **CSV is the authoritative machine-readable export** for Assessment, Evidence, and Gap
Analysis. `reliability-assessment.md` is the human-readable view (index table, per-pillar
detail sections, cross-cutting search paths). Both must contain **identical column text**
per row.

---

## Phase 3 — Generate Deliverables

### 1. `reliability-assessment.md`

Include:

- Assessment metadata (service, date, row count, overall average)
- Note: **Authoritative detail: CSV export; this document is the human-readable view.**
- Matrix definition note (standard vs custom)
- Score key table (from [score-key.md](score-key.md))
- **Cross-cutting evidence locations** table (repo areas searched: source, CI, wiki, etc.)
- Assessment index table (Code, Pillar, Parameter, Score, link to detail sections)
- Assessment table (all columns; **Assessment / Evidence / Gap Analysis must use the
  detailed formats from Step 4**, not one-line summaries):

| Code | Pillar | Parameter | Tier | Condition of Satisfaction | Score | Assessment | Evidence | Gap Analysis |
| ---- | ------ | --------- | ---- | ------------------------- | ----- | ---------- | -------- | ------------ |

- **Per-pillar detail sections** below the index (recommended when rows exceed 20):
  repeat each row as a subsection with full Assessment, Evidence, and Gap Analysis paragraphs

- Each detailed row section must include:
  - What evidence was found (with paths)
  - What evidence was missing (with searches performed)
  - Which parts of the condition were satisfied / not satisfied
  - Numbered remediation steps to reach score 10

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
└── improvement-backlog.md
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

Use this pattern in CSV columns and markdown per-row sections:

```
Score: 6

Assessment:
The condition requires lint config in repo, CI failure on lint errors, and a README-linked
style guide. Ruff and ESLint configs exist (ruff.toml, .eslintrc.json) satisfying the first
requirement. However, Glob `.github/workflows/**` returned zero CI files — lint is never run
on PRs. README.md has no lint section linking the style guide. Score 6 (not 7): configs
exist but enforcement and discoverability gaps remain.

Evidence:
FOUND: ruff.toml (select E4,E7,E9,F,B); .eslintrc.json (extends eslint:recommended)
SEARCHED, NOT FOUND: .github/workflows/* (expected lint job); README.md#linting (expected link)

Gap Analysis:
TO REACH 10:
1. Add `.github/workflows/ci.yml` job `lint` running `ruff check .` and `eslint .` — fail on error
2. Add README "Code Style" section linking ruff.toml and eslint config
3. Verify: open PR with lint violation → CI fails
EFFORT: Small
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
- [ ] Every row has a score plus **detailed** Assessment, Evidence, and Gap Analysis (not one-liners)
- [ ] Every Assessment column explains condition satisfaction and score rationale (3+ sentences)
- [ ] Every Evidence column lists FOUND paths **and** SEARCHED-NOT-FOUND items
- [ ] Every Gap Analysis column has numbered remediation steps with target files/CI jobs
- [ ] `reliability-assessment.csv` column text **matches** the markdown per-row sections exactly
- [ ] `reliability-assessment.md` includes cross-cutting evidence locations and authoritative-detail note
- [ ] `improvement-backlog.md` has tasks for all rows scoring &lt; 8
- [ ] `executive-summary.md` includes pillar/tier breakdowns and maturity band
- [ ] No unsupported claims — every score traceable to evidence or its absence
- [ ] Assessment date and service name recorded
