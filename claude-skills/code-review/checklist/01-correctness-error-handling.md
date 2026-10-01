# 01 — Correctness & Error Handling

The most frequent finding in Tekdi reviews. The question in every item: **what happens when things don't go the expected way?**

### ERR-01 — Every conditional has a defined failure path
**Check:** For each `if` / guard / match on a condition, is the behaviour when the condition is false deliberate and visible — an `else`, an early return, a throw, or a comment explaining why nothing needs to happen?
**Default severity:** BLOCKER
**Applies when:** the change adds or modifies conditionals
**How to verify:** For every new or changed conditional, ask "what if not?" If the answer is "execution silently falls through and continues with an unexpected state", it fails.
**Example (fail → fix):**
```
if (user.isActive) { sendInvoice(user) }
// inactive user: nothing happens, caller assumes invoice sent
```
→ return a result the caller can check, or log + raise, or document why silence is correct.

### ERR-02 — Error paths handle, log, or deliberately propagate
**Check:** Does every caught error end in exactly one of: handled (recovered with a sensible fallback), logged with context and re-raised, or allowed to propagate to a layer that handles it?
**Default severity:** BLOCKER
**Applies when:** the change contains try/catch, error returns, or calls that can fail
**How to verify:** Look for empty catch blocks, catch-and-log-and-continue where continuing produces wrong state, and catch-all handlers that swallow specific errors.

### ERR-03 — No swallowed errors
**Check:** Are there zero empty catch blocks and zero catches that discard the error object?
**Default severity:** BLOCKER
**Applies when:** the change contains error handling
**How to verify:** Search the diff for empty catch bodies, `catch (e) {}`, `except: pass`, `.catch(() => {})`.

### ERR-04 — External calls assume failure
**Check:** Does every call to a network service, database, file system, queue or third-party API handle timeout, non-success response, and malformed response?
**Default severity:** BLOCKER
**Applies when:** the change calls anything outside the process
**How to verify:** For each external call, find the timeout setting, the non-2xx / error-code branch, and validation of the response shape before use.

### ERR-05 — Null / missing values handled at the boundary
**Check:** Where data enters a function from outside (request, DB row, API response, config), are missing or null fields handled before they're dereferenced?
**Default severity:** BLOCKER
**Applies when:** the change reads fields from external or optional data
**How to verify:** Trace each field access on external data back to a check or a typed guarantee.

### ERR-06 — Errors carry enough context to act on
**Check:** Do raised or logged errors say what was being attempted and with which identifiers (not secrets), so someone reading the log can act without re-running?
**Default severity:** SHOULD-FIX
**Applies when:** the change raises or logs errors
**How to verify:** `throw new Error("failed")` fails; `"failed to charge order {orderId}: gateway returned {status}"` passes.

### ERR-07 — Partial failure leaves consistent state
**Check:** If a multi-step operation fails halfway, is state rolled back, compensated, or left in a recoverable, detectable state?
**Default severity:** BLOCKER
**Applies when:** the change performs multiple writes (DB, files, external calls) as one logical operation
**How to verify:** Identify each write; ask what state exists if step N+1 fails. Look for transactions, idempotency keys, or a status field that marks incomplete work.

### ERR-08 — Boundary and edge values handled
**Check:** Are empty collections, zero, negative numbers, very large inputs, and duplicate entries handled correctly?
**Default severity:** SHOULD-FIX
**Applies when:** the change processes collections or numeric input
**How to verify:** Mentally run the code with `[]`, `0`, `-1`, and a duplicate.
