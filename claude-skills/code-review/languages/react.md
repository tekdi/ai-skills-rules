# React

**Inherits:** universal checklist → `javascript-common.md` → this file. If the code is TSX, also run `typescript.md`.

Covers React web and React Native components. Framework-specific concerns (Next.js server components, routing) belong in project-level notes unless they recur across projects.

### REACT-01 — Hooks follow the rules
**Check:** Are hooks called unconditionally at the top level of components/custom hooks — never inside conditions, loops or nested functions?
**Default severity:** BLOCKER
**Applies when:** the change uses hooks
**How to verify:** Usually linter-enforced (`eslint-plugin-react-hooks`); flag if the lint rule is off.

### REACT-02 — Effect dependencies are complete and honest
**Check:** Does each `useEffect` / `useMemo` / `useCallback` list every value it reads, with no disabled `exhaustive-deps` warnings?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds or edits effects/memos
**How to verify:** `// eslint-disable-next-line react-hooks/exhaustive-deps` needs a stated reason; usually it hides a stale-closure bug.

### REACT-03 — Effects clean up and handle races
**Refines:** OPS-12, JS-09
**Check:** Do effects that subscribe, set timers or fetch return a cleanup, and do fetches ignore or abort stale responses (AbortController or an ignore flag)?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds effects with side effects
**How to verify:** A fetch in an effect with no abort lets a slow earlier response overwrite a newer one.

### REACT-04 — Don't use effects for derived state
**Check:** Is state that can be computed from props/other state computed during render (or memoised), rather than synced via `useEffect` + `setState`?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds effects that call setState
**How to verify:** `useEffect(() => setFullName(first + " " + last), [first, last])` fails — just compute it.

### REACT-05 — Loading, empty and error states are rendered
**Refines:** ERR-01, ERR-04
**Check:** Does every component that fetches data render a defined UI for loading, empty result, and failure — not just the success case?
**Default severity:** BLOCKER for user-facing screens; SHOULD-FIX otherwise
**Applies when:** the component fetches or receives async data
**How to verify:** This is the UI form of the missing-else problem. Mentally set the response to `[]`, then to an error.

### REACT-06 — List keys are stable
**Check:** Do lists use stable, unique IDs as `key` — not array index when items can be reordered, inserted or removed?
**Default severity:** SHOULD-FIX
**Applies when:** the change renders lists
**How to verify:** `key={index}` on a mutable list fails.

### REACT-07 — State lives at the right level
**Refines:** SIZ-05
**Check:** Is state kept as local as possible, lifted only as far as needed, and server data held in the project's data layer (React Query/SWR/RTK Query) rather than duplicated into local state?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds state
**How to verify:** The same server data copied into multiple components' `useState` fails. Prop drilling through 3+ levels suggests context or composition.

### REACT-08 — Render performance is not accidentally quadratic
**Check:** Are expensive computations memoised, long lists virtualised, and new object/function props to memoised children avoided in hot paths?
**Default severity:** NITPICK (SHOULD-FIX for lists > a few hundred items)
**Applies when:** the change renders large lists or does heavy computation in render
**How to verify:** Don't demand `useMemo` everywhere — only where there's measurable cost.

### REACT-09 — Components are split by responsibility
**Refines:** SIZ-01, SIZ-02
**Check:** Is data fetching/business logic separated from presentation (custom hook + presentational component), and are components under ~250 lines?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds or grows a component
**How to verify:** A component that fetches, transforms, validates and renders a form in one file fails.

### REACT-10 — Accessibility basics
**Check:** Do interactive elements use semantic elements (`button`, not clickable `div`), have accessible labels, and do images have `alt`?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds UI
**How to verify:** `onClick` on a `div` fails. Icon-only buttons need `aria-label`.

### REACT-11 — No unsafe HTML injection
**Refines:** SEC-02
**Check:** Is `dangerouslySetInnerHTML` avoided, or fed only sanitised content (DOMPurify)?
**Default severity:** BLOCKER
**Applies when:** the change uses `dangerouslySetInnerHTML` or renders user-supplied rich text
**How to verify:** Trace the HTML source back to its origin.
