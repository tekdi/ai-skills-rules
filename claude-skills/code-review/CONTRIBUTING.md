# Contributing to the Code Review Skill

For anyone adding, changing or retiring checks. Read `DESIGN.md` once before your first change — it explains why things are where they are.

## Layout

```
code-review/
├── SKILL.md              # Entry point: when to use, procedure, report format
├── conventions.md        # Severity, verdicts, item format, ID prefixes — single source of truth
├── CONTRIBUTING.md       # This file
├── DESIGN.md             # Why the skill is shaped this way
├── checklist/            # Universal checks — apply to every language
│   ├── 01-correctness-error-handling.md   ERR
│   ├── 02-requirements-alignment.md       REQ
│   ├── 03-extensibility.md                EXT
│   ├── 04-naming-duplication.md           NAM
│   ├── 05-size-structure.md               SIZ
│   ├── 06-testing-depth.md                TST
│   ├── 07-operational-readiness.md        OPS
│   └── 08-security.md                     SEC
└── languages/            # Deltas only — never repeat a universal check
    ├── javascript-common.md   JS    (base for node, typescript, react)
    ├── node.md                NODE
    ├── typescript.md          TS
    ├── react.md               REACT
    ├── python.md              PY
    ├── java.md                JAVA
    └── php.md                 PHP
```

## Where does my check go?

Walk this in order and stop at the first yes:

1. **Is it true in every language?** → `checklist/`, in the section whose question it answers. If it's the same idea expressed differently per language, the idea goes in `checklist/` and each language file gets a `Refines:` item with the concrete form.
2. **Is it true for all of Node, TypeScript and React?** → `languages/javascript-common.md`.
3. **Is it specific to one language or runtime?** → that language's file.
4. **Is it specific to one project or client?** → not here. Put it in that project's own repo (e.g. a `REVIEW.md` the reviewer is told to load). This skill is Tekdi-wide.
5. **Is it enforced by a linter that every project runs?** → probably not here at all. Mention it only if projects commonly have the rule turned off.

If a check doesn't fit any section cleanly, raise it in the PR description rather than creating a new section. A new section needs agreement from the skill owners (see below).

## Writing a check

Use the format from `conventions.md` exactly:

```
### <PREFIX>-<NN> — <short title>
**Refines:** <universal IDs, language files only>
**Check:** <one yes/no question>
**Default severity:** BLOCKER | SHOULD-FIX | NITPICK
**Applies when:** <condition, or "always">
**How to verify:** <what to look at, concretely>
**Example (fail → fix):** <optional>
```

Rules of thumb:

- **One question per item.** If the check has "and" in it, it's probably two items.
- **"Applies when" must be decidable from the diff.** "When performance matters" is not decidable; "when the change contains loops over collections" is.
- **"How to verify" should be something a reviewer can actually do** — a search, a trace, a mental test — not a restatement of the check.
- **Severity reflects production consequence.** Don't mark things BLOCKER because you feel strongly about them.
- **Examples are short.** Five lines of fail, one line of fix. They exist to disambiguate, not to teach.
- **Write for Tekdi's actual stack and failure patterns.** A check that catches something we see every month beats one that's theoretically correct.

## IDs

- Next ID = highest existing number in that file + 1. Never reuse a number.
- Never renumber. Review reports and PR comments reference IDs.
- To remove a check, leave the heading and replace the body with `**Retired:** <date> — <reason>`.
- To split a check, retire the old one and add two new IDs, noting `Replaces: X-NN` on each.

## Adding a new language

1. Create `languages/<language>.md`.
2. Start with the `**Inherits:**` line stating the chain.
3. Pick a new ID prefix and add it to the table in `conventions.md`.
4. Add only deltas. Go through each universal section and ask "is there a language-specific trap here?" — each yes becomes an item with `Refines:`.
5. Add the language to the description in `SKILL.md` frontmatter so the skill triggers for it.
6. If it shares a family with an existing language (e.g. Kotlin with Java), consider a common base file first, as with `javascript-common.md`.

## Tuning thresholds

Numbers in `05-size-structure.md` (function lines, file lines, PR size) are Tekdi defaults. A project that needs different limits states them in its own repo; the reviewer uses the project value and notes it. Change the defaults here only with evidence from real reviews.

## Using review data to improve the skill

The best source of new checks is real reviews:

- When a human reviewer leaves a comment that no checklist item covers, and it's not project-specific, that's a candidate check.
- When a production incident traces back to code that passed review, ask which check would have caught it.
- When a check is marked N/A or PASS in almost every review for months, question whether it earns its place.

Prefer human review comments over AI-generated ones as evidence — AI comments cluster on shallow issues.

## Making a change

1. Branch from `main`: `code-review/<short-description>`.
2. Make the change. Keep one concern per PR (e.g. "add PHP-12", not "add PHP-12 and restructure section 03").
3. In the PR description, state **why** — the real review comment, incident or recurring pattern that prompted it.
4. Run the skill against one recent real PR to confirm the new or changed check produces a sensible verdict.
5. Request review from a skill owner.

## Owners

<!-- Fill in: names/GitHub handles of the people who approve changes to this skill -->
- Ashwin Date (@coolbung)
- 
- 
- 

Changes to `SKILL.md`, `conventions.md`, or the section list need an owner's approval. New or edited items within an existing file need any one other contributor's approval.
