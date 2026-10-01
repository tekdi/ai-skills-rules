# 05 — Size & Structure

Size limits are proxies for "does one unit do one thing". Treat the numbers as triggers for a closer look, not as rules — a 60-line function that reads top to bottom as one step can pass; a 25-line function doing three things can fail.

Thresholds are defaults. Projects may override them in their own repo (see `CONTRIBUTING.md`).

### SIZ-01 — Functions do one thing
**Check:** Can each function's purpose be stated in one sentence without "and"?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds or grows a function
**How to verify:** Trigger for a closer look: **> 40 lines** of logic. Look for comment-separated "sections" inside a function — each is a function waiting to be extracted.

### SIZ-02 — Files have one responsibility
**Check:** Does each file hold one cohesive concept (one class, one adapter, one feature's handlers)?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds to a file
**How to verify:** Trigger: **> 400 lines**. A file growing past this in a PR is the moment to split, not later.

### SIZ-03 — Nesting depth is shallow
**Check:** Is conditional/loop nesting at most 3 levels deep?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds conditionals or loops
**How to verify:** Deep nesting usually means missing early returns or guard clauses.

### SIZ-04 — Parameter lists are short
**Check:** Do functions take **≤ 4** parameters, or group related ones into an object?
**Default severity:** NITPICK
**Applies when:** the change adds or changes function signatures
**How to verify:** Many positional booleans (`send(user, true, false, true)`) fail regardless of count.

### SIZ-05 — Layers are respected
**Check:** Does the code sit in the right layer — no SQL in controllers, no HTTP concerns in domain logic, no business rules in UI components?
**Default severity:** SHOULD-FIX
**Applies when:** the project has a layered structure
**How to verify:** Check imports: a domain module importing an HTTP framework or ORM directly is a smell.

### SIZ-06 — PR is reviewable in one sitting
**Check:** Is the PR focused on one change, small enough to review properly (roughly **< 400 changed lines** excluding generated files and lockfiles)?
**Default severity:** SHOULD-FIX
**Applies when:** always
**How to verify:** A large PR isn't wrong, but review quality collapses with size. Suggest a split along natural seams.
