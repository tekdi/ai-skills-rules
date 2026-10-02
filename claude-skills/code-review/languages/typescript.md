# TypeScript

**Inherits:** universal checklist → `javascript-common.md` → this file. For TypeScript on Node, also run `node.md`; for TSX, also run `react.md`.

The goal: types that prove something. A type that lies is worse than no type.

### TS-01 — No `any`
**Refines:** ERR-05
**Check:** Is `any` absent from new code, with `unknown` used for genuinely unknown data and narrowed before use?
**Default severity:** SHOULD-FIX
**Applies when:** always
**How to verify:** Search the diff for `: any`, `as any`, `<any>`. Each needs a comment justifying it or a fix.

### TS-02 — No unsafe assertions
**Refines:** ERR-05
**Check:** Are `as SomeType` casts and non-null `!` assertions avoided where a type guard or runtime check is possible?
**Default severity:** SHOULD-FIX
**Applies when:** the change uses `as` or `!`
**How to verify:** `response.data as User` on an external response fails — the compiler now believes something nothing verified.

### TS-03 — External data is validated at runtime
**Refines:** SEC-01, ERR-04
**Check:** Is data from HTTP requests, API responses, queues, files and `JSON.parse` validated with a runtime schema (zod, class-validator, io-ts, etc.) before being given a static type?
**Default severity:** BLOCKER
**Applies when:** the change consumes external data
**How to verify:** TypeScript types disappear at runtime. An interface on a request body is not validation.

### TS-04 — Discriminated unions are exhaustively handled
**Refines:** ERR-01, EXT-01
**Check:** Do switches over a union/enum include an exhaustiveness check (`default: assertNever(x)` or `satisfies never`) so adding a new case fails compilation?
**Default severity:** SHOULD-FIX
**Applies when:** the change switches on a union or enum
**How to verify:** Missing `default` or a `default` that silently returns fails.

### TS-05 — Strict compiler settings are not weakened
**Check:** Does the change avoid loosening `tsconfig` (`strict`, `noImplicitAny`, `strictNullChecks`) and avoid `@ts-ignore` (prefer `@ts-expect-error` with a reason)?
**Default severity:** SHOULD-FIX
**Applies when:** the change touches `tsconfig` or adds suppression comments
**How to verify:** Any new `@ts-ignore` or `@ts-nocheck` fails.

### TS-06 — Types model the domain, not the implementation
**Refines:** NAM-01
**Check:** Are illegal states unrepresentable — e.g. a union of `{status: "paid", paidAt: Date} | {status: "pending"}` instead of `{status: string, paidAt?: Date}`?
**Default severity:** NITPICK
**Applies when:** the change introduces types for domain entities
**How to verify:** Optional fields that are only valid in certain states signal a missing union.

### TS-07 — Public function signatures are explicit
**Check:** Do exported functions declare their return types?
**Default severity:** NITPICK
**Applies when:** the change adds exported functions
**How to verify:** Inferred return types on public APIs change silently when the body changes.
