# Node.js

**Inherits:** universal checklist → `javascript-common.md` → this file.

Server-side concerns. Applies to Express, NestJS, Fastify, workers and scripts running on Node.

### NODE-01 — Process-level error handlers exist
**Refines:** ERR-02
**Check:** Does the app register `unhandledRejection` and `uncaughtException` handlers that log and exit (letting the process manager restart), rather than swallowing and continuing?
**Default severity:** SHOULD-FIX
**Applies when:** the change touches app bootstrap
**How to verify:** Continuing after `uncaughtException` leaves the process in unknown state.

### NODE-02 — Framework error handling is used consistently
**Refines:** ERR-02, SEC-06
**Check:** Do route handlers let errors reach the central error middleware / exception filter rather than each formatting its own response?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds routes/controllers
**How to verify:** Express: async handlers wrapped or on Express 5; errors passed to `next(err)`. NestJS: throw `HttpException` subclasses; custom exception filters for domain errors.

### NODE-03 — The event loop is not blocked
**Refines:** OPS-13
**Check:** Are synchronous APIs (`fs.readFileSync`, `crypto.pbkdf2Sync`, `JSON.parse` on very large payloads, CPU-heavy loops) kept out of request paths?
**Default severity:** BLOCKER in request handlers; NITPICK in startup code and CLI scripts
**Applies when:** the change adds sync I/O or heavy computation
**How to verify:** Search for `Sync(` in handlers. CPU-heavy work belongs in a worker thread or queue.

### NODE-04 — Streams handle errors and backpressure
**Refines:** OPS-12, OPS-13
**Check:** Are streams composed with `stream.pipeline` (or `pipeline` from `stream/promises`), not bare `.pipe()` chains that drop errors?
**Default severity:** SHOULD-FIX
**Applies when:** the change uses streams
**How to verify:** `.pipe()` without error handlers on each stage fails.

### NODE-05 — Config is validated at startup
**Refines:** OPS-08
**Check:** Are environment variables read and validated once at boot (e.g. a config module with a schema), not scattered as `process.env.X` through the code?
**Default severity:** SHOULD-FIX
**Applies when:** the change reads `process.env`
**How to verify:** `process.env` referenced outside the config module fails.

### NODE-06 — Graceful shutdown
**Check:** On `SIGTERM`, does the service stop accepting work, finish in-flight requests/jobs within a timeout, and close DB/queue connections?
**Default severity:** SHOULD-FIX
**Applies when:** the change touches server bootstrap or long-running workers
**How to verify:** Look for a `SIGTERM` handler; NestJS `enableShutdownHooks()`.

### NODE-07 — Request-scoped context is propagated
**Refines:** OPS-01
**Check:** Is a request/correlation ID attached to every log line for a request (via `AsyncLocalStorage`, logger child, or framework context)?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds logging in request paths
**How to verify:** Logs within one request share an ID.

### NODE-08 — ORM usage avoids N+1 and unbounded reads
**Refines:** OPS-09, OPS-10
**Check:** With TypeORM/Prisma/Sequelize/Mongoose, are relations loaded explicitly (joins/`include`/`populate` with limits) rather than lazily in loops, and are `find` calls paginated?
**Default severity:** BLOCKER when over unbounded data
**Applies when:** the change uses an ORM/ODM
**How to verify:** `await repo.find()` with no `take`/`limit` on a growing table fails.
