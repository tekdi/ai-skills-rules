# 08 — Security

> **Proposed section — confirm with the team.** Secrets and sensitive logging live in section 07. This section covers trust boundaries: input, identity and access. Kept separate because failures here are almost always blockers and deserve their own pass.

### SEC-01 — Input validated at trust boundaries
**Check:** Is all input from outside the process (request body, query, headers, files, webhooks, queue messages) validated for type, shape, length and allowed values before use?
**Default severity:** BLOCKER
**Applies when:** the change accepts external input
**How to verify:** Look for a schema/validator at the entry point. Validation deep inside business logic is too late.

### SEC-02 — No injection
**Check:** Are queries, shell commands, file paths, templates and URLs built with parameterisation or safe APIs, never by concatenating input?
**Default severity:** BLOCKER
**Applies when:** the change builds SQL/NoSQL queries, shell commands, file paths, HTML or URLs from any variable
**How to verify:** String interpolation into a query or command fails even if "the input is already validated".

### SEC-03 — Authorisation checked on every entry point
**Check:** Does every new endpoint, handler or action check that the caller is allowed to perform it on this specific resource?
**Default severity:** BLOCKER
**Applies when:** the change adds or modifies endpoints/handlers
**How to verify:** Authentication (who are you) is not authorisation (may you do this to that). Look for the check on the resource, not just the route.

### SEC-04 — Tenant and ownership isolation
**Check:** Are records fetched by ID also scoped to the caller's tenant/owner, so one user can't access another's data by changing an ID?
**Default severity:** BLOCKER
**Applies when:** the change fetches or mutates records by identifier in a multi-user or multi-tenant system
**How to verify:** `findById(req.params.id)` without a tenant/owner condition fails.

### SEC-05 — Sensitive data not over-exposed in responses
**Check:** Do API responses return only fields the caller needs — no password hashes, internal IDs, other users' PII, or full DB rows?
**Default severity:** BLOCKER
**Applies when:** the change returns entities in responses
**How to verify:** Returning ORM entities directly instead of a DTO/serializer is the usual cause.

### SEC-06 — Errors don't leak internals
**Check:** Do user-facing errors avoid stack traces, SQL, file paths and internal service names?
**Default severity:** SHOULD-FIX
**Applies when:** the change returns errors to clients
**How to verify:** Check the error handler, not just individual throws.

### SEC-07 — Dependencies are justified and safe
**Check:** Is each new dependency necessary, maintained, license-compatible, and free of known critical vulnerabilities?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds dependencies
**How to verify:** Check last release date, weekly downloads/usage, open advisories, license. Pin versions per project policy.

### SEC-08 — File uploads are constrained
**Check:** Are uploads limited by size and type (checked by content, not just extension), stored outside the web root, and given generated names?
**Default severity:** BLOCKER
**Applies when:** the change accepts file uploads
**How to verify:** Trace an uploaded file from request to storage.
