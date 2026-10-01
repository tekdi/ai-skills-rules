# 06 — Testing Depth

Coverage is weak across Tekdi projects, and where tests exist they mostly assert the happy path. The question isn't "are there tests" — it's **"would a test fail if this behaviour broke?"**

### TST-01 — Changed behaviour has a test that would catch its breakage
**Check:** For each behaviour added or changed, is there a test that fails if the behaviour is reverted or broken?
**Default severity:** SHOULD-FIX (BLOCKER for payments, auth, data deletion, or anything in the requirement's acceptance criteria)
**Applies when:** the change alters behaviour (not pure refactor, docs, or config)
**How to verify:** Mentally revert the key line. Would any test go red?

### TST-02 — Failure paths are tested
**Check:** For each error path added (see section 01), is there a test that triggers it and asserts the outcome?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds error handling or conditionals
**How to verify:** Count happy-path tests vs failure-path tests for the changed code. All-happy fails.

### TST-03 — Assertions are meaningful
**Check:** Do tests assert on outcomes (returned values, state changes, calls made with specific arguments) rather than only "didn't throw" or "was called"?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds or modifies tests
**How to verify:** Tests with no assertion, `expect(result).toBeDefined()`, `assert response is not None`, or snapshot-only tests of logic fail.

### TST-04 — Tests are independent and deterministic
**Check:** Do tests avoid depending on execution order, wall-clock time, randomness, or real network calls?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds or modifies tests
**How to verify:** Look for shared mutable fixtures, `sleep`, `Date.now()`/`datetime.now()` without injection, calls to real external URLs.

### TST-05 — Edge cases from section 01 are tested
**Check:** Are empty, zero, null, boundary and duplicate inputs covered where relevant?
**Default severity:** NITPICK
**Applies when:** the change processes collections or numeric/optional input
**How to verify:** Cross-reference ERR-05 and ERR-08.

### TST-06 — Test names describe the scenario
**Check:** Does each test name state the condition and expected outcome (`returns 404 when order belongs to another tenant`)?
**Default severity:** NITPICK
**Applies when:** the change adds tests
**How to verify:** `test1`, `testOrder`, `it works` fail.

### TST-07 — Bug fixes include a regression test
**Check:** If the PR fixes a bug, is there a test that reproduces the bug and now passes?
**Default severity:** SHOULD-FIX
**Applies when:** the PR is a bug fix
**How to verify:** The test should fail on the parent commit.
