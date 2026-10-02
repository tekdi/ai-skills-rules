# Conventions

Shared definitions referenced by every checklist and language file. Change these here only — never redefine them elsewhere.

## Severity

| Severity | Meaning | Merge impact |
|----------|---------|--------------|
| `BLOCKER` | Will cause wrong behaviour, data loss, a security exposure, or an outage in production. Or the change doesn't do what the requirement asked. | Must be fixed before merge. |
| `SHOULD-FIX` | Won't break today, but raises the cost or risk of the next change: poor extensibility, missing tests for failure paths, unclear naming, oversized units. | Fix in this PR unless there's a recorded reason to defer (a linked follow-up ticket). |
| `NITPICK` | Style or preference with no functional or maintenance consequence. | Author's call. |

Each checklist item has a **default severity**. The reviewer may raise or lower it with a one-line justification — e.g. a missing error path in a throwaway script can drop to `SHOULD-FIX`; a naming issue on a public API can rise to `BLOCKER`.

## Verdicts

- `PASS` — holds.
- `FAIL` — doesn't hold; needs severity, `file:line`, and a fix.
- `N/A` — doesn't apply to this change; needs a one-line reason.

An item is `N/A` only when its **Applies when** condition is false. If the condition is true and the reviewer didn't check, that is not N/A.

## Item format

Every checklist item, universal or language-specific, uses this shape:

```
### <PREFIX>-<NN> — <short title>
**Check:** <one question the reviewer answers yes/no>
**Default severity:** BLOCKER | SHOULD-FIX | NITPICK
**Applies when:** <condition; "always" if unconditional>
**How to verify:** <what to look at, concretely>
**Example (fail → fix):** <optional, short>
```

## ID prefixes

| Prefix | Section |
|--------|---------|
| `ERR` | 01 Correctness & error handling |
| `REQ` | 02 Requirements alignment |
| `EXT` | 03 Extensibility |
| `NAM` | 04 Naming & duplication |
| `SIZ` | 05 Size & structure |
| `TST` | 06 Testing depth |
| `OPS` | 07 Operational readiness |
| `SEC` | 08 Security |
| `JS`  | languages/javascript-common |
| `NODE`, `TS`, `REACT`, `PY`, `JAVA`, `PHP` | languages/<file> |

IDs are permanent. When an item is retired, mark it `**Retired:** <reason>` rather than deleting it, so historical review reports still resolve.
