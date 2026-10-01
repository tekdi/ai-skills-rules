# 02 — Requirements Alignment

Does the code do what was asked — all of it, and nothing it wasn't asked to do?

**Section applicability:** if the PR has no linked requirement, or the link can't be resolved, mark every item in this section `N/A — requirements not available` and move on. Do not guess requirements from the code.

### REQ-01 — Every acceptance criterion is implemented
**Check:** For each acceptance criterion in the linked requirement, can you point to the code that implements it?
**Default severity:** BLOCKER
**Applies when:** requirements resolved
**How to verify:** List the criteria. Map each to `file:line`. Any criterion with no mapping fails.

### REQ-02 — Unhappy paths in the requirement are implemented
**Check:** Where the requirement describes what should happen on invalid input, missing data, permission denial or failure, does the code do exactly that?
**Default severity:** BLOCKER
**Applies when:** requirements resolved and they describe any failure behaviour
**How to verify:** Requirements often state error behaviour in passing ("user sees a message if…"). Find each such statement and its implementation.

### REQ-03 — No unrequested behaviour
**Check:** Is every behaviour change in the diff traceable to the requirement, or explained in the PR description?
**Default severity:** SHOULD-FIX
**Applies when:** requirements resolved
**How to verify:** Look for changes unrelated to the ticket — refactors, "while I was here" fixes, new config flags. They aren't wrong, but they need to be called out so they're reviewed on their own merits.

### REQ-04 — Ambiguities are surfaced, not silently resolved
**Check:** Where the requirement is ambiguous, has the author stated which interpretation they chose (PR description or code comment)?
**Default severity:** SHOULD-FIX
**Applies when:** requirements resolved and contain ambiguity the code had to resolve
**How to verify:** If the reviewer can read the requirement two ways and the code picks one without comment, it fails.

### REQ-05 — Tests map to acceptance criteria
**Check:** Is there at least one test per acceptance criterion?
**Default severity:** SHOULD-FIX
**Applies when:** requirements resolved
**How to verify:** Cross-reference with section 06. Name the test for each criterion.
