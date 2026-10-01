# 07 — Operational Readiness

Problems that only surface in production: an incident nobody can debug, a secret in the repo, a query that works on 10 rows and dies on 100,000.

## Logging & observability

### OPS-01 — Significant operations are logged with context
**Check:** Are state-changing operations, external calls, and failures logged with identifiers (request ID, tenant, entity ID) sufficient to trace one request end to end?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds business operations, external calls, or error handling
**How to verify:** Imagine a support ticket saying "order 1234 failed at 3pm". Could you find it in the logs from what this code writes?

### OPS-02 — Log levels are appropriate
**Check:** Errors at error level, expected conditions at info/debug, no noisy logs in hot loops?
**Default severity:** NITPICK
**Applies when:** the change adds logging
**How to verify:** Expected validation failures logged as `error` fail. Per-item logs inside a loop over thousands of records fail.

### OPS-03 — No sensitive data in logs
**Check:** Are passwords, tokens, OTPs, full card numbers, Aadhaar/ID numbers and other PII excluded or masked in logs?
**Default severity:** BLOCKER
**Applies when:** the change logs request bodies, user objects, headers, or external responses
**How to verify:** Logging a whole request/user/response object is the usual leak.

### OPS-04 — Structured logging used where the project supports it
**Check:** Are logs written via the project's logger with structured fields rather than `print`/`console.log`/string concatenation?
**Default severity:** NITPICK
**Applies when:** the project has a logger configured
**How to verify:** Search the diff for `print(`, `console.log(`, `System.out.println`, `var_dump`, `echo` used for logging.

## Configuration & secrets

### OPS-05 — No secrets in code
**Check:** Are there zero credentials, API keys, tokens, private keys or connection strings with passwords in the diff — including tests, fixtures and config files?
**Default severity:** BLOCKER
**Applies when:** always
**How to verify:** Search for `key`, `secret`, `token`, `password`, long base64/hex strings. A secret committed once is compromised even if removed in a later commit — it must be rotated.

### OPS-06 — Environment-specific values come from environment/config
**Check:** Are URLs, hostnames, ports, bucket names, feature flags, limits and provider selection read from environment variables or config, not hard-coded?
**Default severity:** SHOULD-FIX
**Applies when:** the change references anything that differs between dev, staging and production
**How to verify:** Literal `localhost`, staging URLs, or numeric limits in code fail.

### OPS-07 — New configuration is documented
**Check:** Is every new environment variable added to `.env.example` (or equivalent) with a description and safe default?
**Default severity:** SHOULD-FIX
**Applies when:** the change introduces new config
**How to verify:** Diff should touch the example env file whenever it reads a new variable.

### OPS-08 — Missing config fails fast
**Check:** Does the application fail at startup with a clear message when required config is missing, rather than failing later at first use?
**Default severity:** SHOULD-FIX
**Applies when:** the change introduces required config
**How to verify:** Look for validation at boot, not `process.env.X` read deep inside a request handler with no check.

## Performance under real load

### OPS-09 — No I/O inside loops
**Check:** Are there no database queries, API calls or file reads executed once per iteration of a loop (the N+1 pattern)?
**Default severity:** BLOCKER when the loop is over user data or unbounded input; SHOULD-FIX otherwise
**Applies when:** the change contains loops or maps over collections
**How to verify:** Inside every loop body, look for `await`, repository/ORM calls, `fetch`, HTTP clients. Lazy-loaded ORM relations accessed in a loop count too. Fix with batching, `IN` queries, eager loading, or bulk APIs.

### OPS-10 — Queries are bounded
**Check:** Do list queries have pagination or a limit, and do they filter on indexed columns?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds or modifies queries
**How to verify:** `SELECT *` with no `LIMIT` on a growing table fails. New `WHERE` columns should have an index or a migration adding one.

### OPS-11 — Timeouts and retries are explicit
**Check:** Do external calls set a timeout, and do retries (if any) use backoff and a cap, only for idempotent operations?
**Default severity:** SHOULD-FIX
**Applies when:** the change calls external services
**How to verify:** Default HTTP client timeouts are often infinite. Retrying a non-idempotent POST can double-charge.

### OPS-12 — Resources are released
**Check:** Are connections, file handles, streams and locks closed/released on every path, including errors?
**Default severity:** SHOULD-FIX
**Applies when:** the change opens resources
**How to verify:** Look for the language's scoped-release construct (see language files).

### OPS-13 — Large data is streamed or chunked
**Check:** Are large files, exports and bulk operations processed in chunks rather than loaded fully into memory?
**Default severity:** SHOULD-FIX
**Applies when:** the change handles uploads, exports, reports, or bulk records
**How to verify:** Reading an entire file or table into an array before processing fails when size is unbounded.
