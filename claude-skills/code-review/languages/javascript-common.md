# JavaScript — Common

**Inherits:** all universal checklist sections. Applies to every JS-family change. `node.md`, `typescript.md` and `react.md` build on this file — run this first.

Only items that are specific to, or sharper in, JavaScript live here. Each notes the universal item it refines.

### JS-01 — Every promise is awaited or handled
**Refines:** ERR-02, ERR-03
**Check:** Is every promise either `await`ed inside a try/catch (or a function whose caller handles rejection), returned, or given a `.catch()`? No floating promises.
**Default severity:** BLOCKER
**Applies when:** the change calls async functions
**How to verify:** Calls to async functions with no `await`, `return` or `.catch` fail. `array.forEach(async ...)` fails — errors vanish and completion isn't awaited.

### JS-02 — `.catch(() => {})` and empty catch are absent
**Refines:** ERR-03
**Check:** No swallowed rejections.
**Default severity:** BLOCKER
**Applies when:** the change has async error handling
**How to verify:** Search for `.catch(() => {})`, `.catch(() => null)`, `catch (e) {}`.

### JS-03 — Parallel work uses the right combinator
**Refines:** OPS-09
**Check:** Are independent async calls run concurrently (`Promise.all` / `Promise.allSettled`) rather than awaited one by one in a loop — and is the choice between `all` (fail fast) and `allSettled` (collect all outcomes) deliberate?
**Default severity:** SHOULD-FIX
**Applies when:** the change awaits inside a loop
**How to verify:** `for (...) { await ... }` over independent items fails unless ordering or rate limiting is required (say so in a comment). Unbounded `Promise.all` over thousands of items needs a concurrency limit.

### JS-04 — Null and undefined handled explicitly
**Refines:** ERR-05
**Check:** Is optional data accessed with `?.` / `??` or an explicit guard, and is `??` used (not `||`) when `0`, `""` or `false` are valid values?
**Default severity:** SHOULD-FIX
**Applies when:** the change reads optional fields
**How to verify:** `const limit = input.limit || 10` breaks when `limit` is `0`.

### JS-05 — Strict equality
**Check:** `===` / `!==` used; `==` only for the deliberate `x == null` idiom.
**Default severity:** NITPICK
**Applies when:** always
**How to verify:** Usually linter-enforced; flag only if the repo has no linter.

### JS-06 — Only `Error` objects are thrown
**Refines:** ERR-06
**Check:** Are thrown values `Error` instances (or subclasses) so stack traces survive?
**Default severity:** SHOULD-FIX
**Applies when:** the change throws
**How to verify:** `throw "failed"` or `throw { code: 1 }` fail.

### JS-07 — Dependency hygiene
**Refines:** SEC-07
**Check:** Is the lockfile updated alongside `package.json`? Is the package placed in `dependencies` vs `devDependencies` correctly? Is a heavy library avoided where a few lines or a platform API would do?
**Default severity:** SHOULD-FIX
**Applies when:** `package.json` changes
**How to verify:** Lockfile diff present; test/build tools not in `dependencies`; no `lodash` for one `pick`.

### JS-08 — No mutation of shared inputs
**Check:** Do functions avoid mutating objects/arrays passed in, unless that's their documented purpose?
**Default severity:** SHOULD-FIX
**Applies when:** the change modifies objects or arrays received as arguments
**How to verify:** `.sort()`, `.reverse()`, `.splice()`, `Object.assign(arg, …)` on parameters mutate the caller's data.

### JS-09 — Timers and listeners are cleaned up
**Refines:** OPS-12
**Check:** Are `setInterval`, `setTimeout`, event listeners and subscriptions cleared when no longer needed?
**Default severity:** SHOULD-FIX
**Applies when:** the change registers timers or listeners
**How to verify:** Each registration has a matching clear/remove on the teardown path.

### JS-10 — Dates and numbers handled safely
**Check:** Are dates handled with explicit timezones (IST vs UTC) and money handled in integer minor units (paise) or a decimal library, never floating point?
**Default severity:** BLOCKER for money; SHOULD-FIX for dates
**Applies when:** the change handles currency or dates
**How to verify:** `0.1 + 0.2` arithmetic on amounts fails. `new Date(string)` without a timezone on user-facing dates is suspect.
