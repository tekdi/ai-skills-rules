---
name: backend-bug-solver
description: >
  Agentic backend bug investigation skill for REST API issues inside a service codebase.
  Supports any database via MCP tools. Trigger this skill whenever the user says things
  like "I have a bug", "bug assigned to me", "debug this API", "something is wrong with
  my endpoint", "fix this backend issue", "investigate this error", "API is returning
  wrong data", or pastes a curl command and says something is broken. Always use this
  skill when invoked inside a service directory (e.g. user-service, order-service) — the
  codebase context is essential and this skill knows how to use it. This skill takes over
  the full debugging workflow: it maps the project structure, traces the code path from
  route → controller → service → DB query, then investigates via MCP if available —
  the user just answers questions and reviews findings.
---

# Backend Bug Solver

An agentic skill that turns a bug report into a structured investigation using the local
codebase, and optionally the DB via MCP tools.

**Golden rule: Code is the primary source of truth. DB via MCP is used only to confirm
what the code tells you.**

---

## Phase 1 — Collect Bug Context (ask this first, before any code scanning)

Ask the user these 3 questions in a single message:

```
To investigate the bug, I need 3 things:

1. What's the issue? (what should happen vs. what actually happens)
2. Paste the curl command for the failing request.
3. Paste the API response you're getting.
```

Wait for their response before doing anything else.

---

## Phase 2 — Targeted Code Trace

Extract the HTTP method and endpoint path from the curl. Now do a **targeted search** — no broad project scanning.

**Detect DB type from the code** — grep ORM imports in source files only:

| Code clue | DB |
|-----------|----|
| `typeorm` with `postgres`, `pg` | PostgreSQL |
| `mysql2`, `mariadb`, `sequelize` with `dialect: 'mysql'` | MySQL/MariaDB |
| `mongoose`, `mongodb` | MongoDB |
| `sqlite3`, `better-sqlite3`, `prisma` with `provider = "sqlite"` | SQLite |
| `mssql`, `tedious` | MSSQL |
| `ioredis`, `redis` as primary store | Redis |

**Check for DB MCP tools** — look for any available MCP tools matching:
`mcp__postgres__*`, `mcp__mysql__*`, `mcp__mongodb__*`, `mcp__sqlite__*`,
`mcp__supabase__*`, `mcp__neon__*`, `mcp__db__*`, `execute_query`, `run_sql`.

Note whether MCP is available — this determines Phase 3 path.

---

Trace using only targeted greps based on the known endpoint:

1. **Find the route** — `grep -r "<METHOD> /<path>"` in source files only
2. **Find the controller/handler** — what params it reads, what it calls, what it returns
3. **Follow the service layer** — business logic, DB calls, transformations
4. **Find the DB query** — the exact `WHERE`, `JOIN`, `SELECT`, `ORDER BY`, `LIMIT`

After this step, state clearly: **what the code intends to do** and **your hypothesis for why it's wrong**.

---

## Phase 3 — Database Verification

### If MCP tools ARE available

Use MCP to confirm your hypothesis. Apply the queries matching the detected DB type:

**PostgreSQL / MySQL / MSSQL / SQLite**
- Confirm schema: check that columns the code references actually exist and have the right types
- Reproduce the query: run the translated SQL with the same parameters from the curl
- Compare: does the raw DB result match the API response?

**MongoDB**
- Inspect the document shape: `findOne()` to verify actual field names
- Reproduce the filter: run `find({ <filter_from_code> })` directly
- Compare: does the raw result match the API response?

**Redis**
- Inspect the key: check `TYPE`, `TTL`, and value for the key the code constructs
- Compare: does the stored value match what the response should be?

**Common patterns to check across all DBs:**
| Symptom | Check |
|---------|-------|
| Missing rows/documents | Soft-delete filter, tenant/org filter, wrong JOIN |
| Wrong count | Duplicate rows from JOIN — use `DISTINCT` or `$group` |
| Null fields | Missing JOIN, wrong field name/alias |
| Wrong user's data | Missing `user_id` / `org_id` / `tenantId` filter |
| Stale data | Cache not invalidated |
| Date/time off | Timezone handling in DB vs application |
| Intermittent failures | Missing index — run `EXPLAIN` / `explain("executionStats")` |

### If NO MCP tools are available

Complete the full code trace, then stop and report what was found from code alone.
Do NOT attempt shell CLI tools or read env files.

---

## Phase 4 — Pinpoint the Bug

Cross-reference code trace + DB findings + API response to classify:

- **Code bug**: wrong field, missing filter, bad serialization, off-by-one
- **Data bug**: unexpected null, missing record, wrong value in DB
- **Schema bug**: column/field missing or type mismatch
- **Integration bug**: code and DB both correct, but transformation between layers is wrong

---

## Phase 5 — Report

```
## Bug Investigation Report

**Service:** <service name>
**Endpoint:** <METHOD /path>
**Bug:** <one-line: expected vs. actual>

### Root Cause
<Precise explanation — file:line, what is wrong>

### Code Path
Route → Controller → Service → DB query (file:line references)

### Evidence
- Code: <what the code does wrong>
- DB (if MCP used): <what the data shows>
- Response: <what doesn't match>

### Fix
<File path, what to change, code snippet>

### How to Verify
<Query or curl to confirm it's fixed>

### MCP Deeper Debug (if MCP was NOT available)
To confirm this hypothesis, run:
<exact query the developer should run manually>
```

---

## Behavioral Rules

- **Code first, always.** Trace the full code path before touching the DB.
- **Hypothesis before querying.** State what you think the bug is, then confirm with MCP.
- **Use file:line references.** Name the exact file and line when pointing to a bug.
- **MCP only for DB.** Never read env files or config to detect DB — use code imports and ORM files only.
- **No MCP = code + response analysis only.** Report findings and provide the manual query the developer can run.
- **One follow-up question at a time.** Never dump a list mid-investigation.
- **If the bug isn't found:** list top 3 hypotheses with evidence for/against and the next concrete step for each.
