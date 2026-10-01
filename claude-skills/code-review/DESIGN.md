# Design Rationale

Why the code review skill is shaped this way. Read before proposing structural changes — most of these decisions were deliberate trade-offs, and reversing one usually reintroduces the problem it solved.

## Checklist, not scoring

**Decision:** Each item gets PASS / FAIL / N/A. No numeric scores.

**Why:** Scores invite debate about whether something is a 3 or a 4 instead of fixing it. A checklist produces actionable findings. Aggregate numbers (count of blockers, should-fixes) come for free from the verdicts.

**Rejected alternative:** a weighted rubric producing an overall quality score. It looked useful for dashboards but made reviews slower and less concrete.

## Severity on every item

**Decision:** Every item has a default severity; reviewers can adjust with a reason.

**Why:** A flat checklist doesn't tell the author what actually stops a merge. Default severity makes the common case consistent across reviewers; the override keeps it from being rigid where context matters (throwaway script vs payment flow).

## N/A must be explicit

**Decision:** Every item gets a verdict. N/A requires a reason and is only valid when the "Applies when" condition is false.

**Why:** Silent skipping is indistinguishable from not looking. Forcing an explicit N/A makes the reviewer — human or AI — consciously decide a check doesn't apply. It also makes the report auditable: you can see what was considered.

**Rejected alternative:** conditional checks that hide themselves when not applicable. Simpler to read, but hides reviewer omissions.

## Universal first, then language deltas

**Decision:** `checklist/` holds language-neutral checks. `languages/` files contain only additions and language-specific sharpenings, each pointing to the universal item it refines.

**Why:** The failure patterns Tekdi sees — missing else paths, swallowed errors, N+1 queries, vague names — are the same in every language; only their surface form differs. Writing full per-language checklists would mean six copies of "handle the error path" that drift apart as people edit one and not the others.

**Consequence:** a language file is short and only makes sense alongside the universal checklist. That's intended.

## A shared JavaScript base

**Decision:** `javascript-common.md` is inherited by Node, TypeScript and React.

**Why:** Promise handling, null/undefined, dependency hygiene and similar concerns apply to all three. Without a shared base, the same checks would be copied into three files and diverge.

**Chain:** universal → `javascript-common` → `node` / `typescript` / `react`. A TSX file on the frontend runs `typescript` and `react`; a NestJS service runs `typescript` and `node`.

## One file per universal section

**Decision:** Eight section files rather than one big checklist.

**Why:** A reviewer (or the model) can load only the sections relevant to a narrow change — e.g. just operational readiness for a config change. It also reduces merge conflicts when several people edit the skill at once.

## Why these eight sections

The sections reflect where Tekdi reviews actually find problems, in roughly the order of frequency:

1. **Correctness & error handling** — the most common finding: conditionals with no defined failure path, swallowed exceptions.
2. **Requirements alignment** — code that works but doesn't do what the ticket asked. Degrades to N/A when requirements aren't resolvable, so it never blocks a review.
3. **Extensibility** — framed as "closed vs open set", not branch count. A switch over days of the week is fine; a switch over payment providers should be adapters.
4. **Naming & duplication** — treated as one section because in practice they're one problem: near-identical micro-functions with names too vague to distinguish.
5. **Size & structure** — numeric thresholds are triggers for a closer look, not rules.
6. **Testing depth** — the question is "would a test fail if this broke", not "are there tests". Coverage is weak, and existing tests over-index on the happy path.
7. **Operational readiness** — logging, config/secrets, and load behaviour. Grouped because all three only surface in production.
8. **Security** — trust boundaries, identity and access. Separated from operational readiness because almost everything in it is a blocker.

## Dead code distinguishes caution from neglect

**Decision:** NAM-07 accepts retained old code if it's explained with a reason and a removal ticket.

**Why:** Dead code is often left deliberately because the author couldn't safely test removing the old path. That's a legitimate call; the problem is when it's undocumented. Unexplained dead code gets deleted; explained dead code gets tracked.

## Requirements degrade gracefully

**Decision:** If the linked requirement can't be resolved, section 02 is N/A and the review continues.

**Why:** Requirements live in different tools across projects (Jira, GitHub issues, Google Docs, BRDs) and aren't always reachable. Blocking the whole review on that would make the skill unusable in practice. The code can't be the source of its own requirements, so the skill doesn't guess.

## Stable IDs

**Decision:** IDs are never renumbered or reused; retired items stay as tombstones.

**Why:** Review reports, PR comments and any future analytics reference IDs. Renumbering would silently break every past reference.

## What this skill deliberately does not do

- **Replace linters.** Checks a linter enforces are only included when projects commonly have the rule disabled.
- **Review architecture or design docs.** That's `tech-design-review`.
- **Hold project-specific rules.** Those belong in the project's own repo.
- **Judge style preferences.** Formatting, brace style, quote style — leave to formatters.

## Open questions

Decisions not yet made; revisit once the skill has been used on real PRs:

- Whether section 08 (Security) stays separate or merges into 07.
- Whether to calibrate checks against historical PR review comments from Tekdi repos, and which repos.
- Whether size thresholds should differ for frontend vs backend.
- Whether to emit machine-readable output (JSON) alongside the markdown report for tracking trends.
