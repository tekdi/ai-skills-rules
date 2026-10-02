# 04 — Naming & Duplication

In Tekdi reviews these are usually the same problem: several near-identical small functions in one file, with names too vague to tell them apart. If you can't name the difference, there may not be one.

### NAM-01 — Names state intent, not mechanics
**Check:** Do function, variable and module names say what the thing is for, rather than how it works or its type?
**Default severity:** SHOULD-FIX
**Applies when:** always
**How to verify:** `processData`, `handleItem`, `temp`, `data2`, `helper`, `utils2` fail. `calculateLateFee`, `pendingInvoices` pass.

### NAM-02 — Similar functions have names that make the difference obvious
**Check:** Where a module has several functions doing similar things, does each name make the distinction clear without reading the body?
**Default severity:** SHOULD-FIX
**Applies when:** the module contains two or more functions with overlapping purpose
**How to verify:** Read only the names. If you can't predict which one to call for a given case, it fails.
**Example (fail → fix):** `getUser`, `fetchUser`, `loadUser`, `getUserData` in one file → decide what actually differs (cache vs DB, with vs without relations) and name for that — or merge.

### NAM-03 — Near-duplicate functions are merged
**Check:** Are there functions whose bodies differ only in a value, a field name, or one step, that should be one function with a parameter?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds a function similar to an existing one in the same module or nearby
**How to verify:** Diff similar functions against each other mentally. If the difference fits in a parameter, merge. If you cannot name the distinction between them (NAM-02), that's the signal.

### NAM-04 — No copy-paste across modules
**Check:** Has logic been copied from another file rather than extracted to a shared place?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds logic
**How to verify:** Search the repo for a distinctive line from the new code.

### NAM-05 — Consistent vocabulary
**Check:** Is the same concept named the same way throughout (not `customer` in one place, `client` in another, `user` in a third)?
**Default severity:** NITPICK
**Applies when:** always
**How to verify:** Compare new names against the existing domain terms in the codebase and the requirement.

### NAM-06 — Booleans read as questions
**Check:** Are boolean variables and functions named as predicates (`isExpired`, `hasAccess`, `shouldRetry`)?
**Default severity:** NITPICK
**Applies when:** the change introduces booleans
**How to verify:** `status`, `flag`, `check` as booleans fail.

### NAM-07 — Dead code removed or explained
**Check:** When the change replaces behaviour, is the old code deleted — or, if deliberately kept (e.g. old path not yet safe to remove), marked with a comment giving the reason and a removal ticket?
**Default severity:** SHOULD-FIX
**Applies when:** the change modifies or replaces existing code
**How to verify:** Look for commented-out blocks, unreachable branches, unused imports/helpers, and functions with no remaining callers. Unexplained → delete. Explained with a ticket → pass.
