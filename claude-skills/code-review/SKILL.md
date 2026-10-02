---
name: code-review
description: Review a pull request, diff or set of files against Tekdi's code review checklist — universal checks first, then language-specific deltas for Python, Java, PHP, Node.js, TypeScript and React. Use when asked to review code, review a PR, check a diff, or assess code quality before merge.
---

# Code Review

A checklist-driven review. Every item gets an explicit verdict. Nothing is skipped silently.

## When to use

- Reviewing a pull request, branch, diff, or a set of files before merge
- Self-review before raising a PR
- Auditing an existing module for quality

Not for: architecture or design-document review (use `tech-design-review`), or debugging a specific bug (use `backend-bug-solver` / `frontend-bug-solver`).

## Inputs

1. **The change** — PR, diff, branch, or files. Required.
2. **Requirements** — ticket, issue, spec or BRD linked from the PR. Optional. If the link resolves, section 02 is checked against it. If it doesn't resolve or isn't present, section 02 is marked `N/A — requirements not available` and the review continues.

## Procedure

1. **Read `conventions.md`** — severity levels, verdicts, and the item format.
2. **Scope the change.** List the files changed and the languages involved. Note whether the change is new code, a modification, or a deletion — some checks (e.g. dead code) only bite on modifications.
3. **Resolve requirements.** Try to fetch the linked ticket/spec. Summarise the acceptance criteria in a few lines if found.
4. **Run the universal checklist**, in order, from `checklist/`:
   - `01-correctness-error-handling.md`
   - `02-requirements-alignment.md`
   - `03-extensibility.md`
   - `04-naming-duplication.md`
   - `05-size-structure.md`
   - `06-testing-depth.md`
   - `07-operational-readiness.md`
   - `08-security.md`
5. **Run the language deltas** for each language in the change, from `languages/`. For Node.js, TypeScript and React, run `javascript-common.md` first, then the specific file.
6. **Write the report** in the format below.

For a narrow change, a reviewer may load only the relevant sections — but every item in a loaded section still gets a verdict.

## Verdicts

Every item gets exactly one:

- `PASS` — the check holds.
- `FAIL` — the check doesn't hold. Must carry a severity, a location (`file:line`), and a concrete fix.
- `N/A` — the check does not apply to this change. Must carry a one-line reason.

Never leave an item without a verdict. "Didn't look" is not N/A.

## Report format

```
## Summary
Merge recommendation: BLOCK | MERGE AFTER FIXES | MERGE
Blockers: <n>   Should-fix: <n>   Nitpicks: <n>
Requirements: <resolved: link | not available>

## Findings (FAIL only, ordered by severity)
### [BLOCKER] ERR-02 — Unhandled else path in payment status check
Location: src/payments/status.ts:48
Problem: <what is wrong and why it matters>
Fix: <concrete change>

## Checklist
| ID | Verdict | Note |
|----|---------|------|
| ERR-01 | PASS | |
| REQ-01 | N/A | requirements not linked |
...
```

Merge recommendation rules:
- Any `BLOCKER` → `BLOCK`
- No blockers, any `SHOULD-FIX` → `MERGE AFTER FIXES`
- Only nitpicks or nothing → `MERGE`

## Principles for the reviewer

- Cite the line. A finding without a location is an opinion.
- Propose the fix, don't just name the problem.
- One finding per root cause. If five functions share the same missing error path, that's one finding listing five locations.
- Don't restate what a linter already enforces unless the linter is not configured in the repo.
- Severity reflects consequence in production, not how much the reviewer dislikes it.
