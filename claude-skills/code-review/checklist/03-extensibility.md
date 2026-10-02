# 03 — Extensibility

Can the next foreseeable change be made by **adding** code rather than **editing** code that already works?

The test is not "how many branches" — it's "is this set of cases closed or open?"
- **Closed sets** (days of the week, HTTP methods, a fixed enum defined by a standard): a switch/if-chain is fine.
- **Open sets** (payment gateways, notification channels, report formats, file importers, SMS providers, LLM providers, tenant-specific rules): new cases will arrive. They need a structure where a new case is a new unit.

### EXT-01 — Open sets use an extensible structure
**Check:** Where code branches on a type, provider, category or channel that is likely to grow, is each case a separate unit (adapter, strategy, handler, plugin) selected via a registry or map, rather than branches in one function?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds or extends branching on a type/category/provider
**How to verify:** Ask: "Is it plausible a new case gets added in the next year?" If yes and adding it means editing the existing switch, it fails.
**Example (fail → fix):**
```
switch (gateway) {
  case "razorpay": ...40 lines...
  case "stripe":   ...40 lines...
  case "paytm":    ...40 lines...
}
```
→ a `PaymentGateway` interface, one adapter file per gateway, and a registry `{ razorpay: RazorpayAdapter, ... }`. Adding a gateway = adding a file + one registry line.

### EXT-02 — Adding a case to an existing switch on an open set
**Check:** If the diff adds a new branch to an existing switch over an open set, has the author either refactored to adapters or linked a follow-up ticket to do so?
**Default severity:** SHOULD-FIX
**Applies when:** the diff adds a case to existing branching
**How to verify:** The third case added to a switch is the usual moment to refactor. The fifth is overdue.

### EXT-03 — Provider-specific values come from configuration
**Check:** Are provider names, endpoints, limits, feature toggles and per-tenant behaviour read from config/environment rather than hard-coded?
**Default severity:** SHOULD-FIX
**Applies when:** the change involves external providers, limits, or tenant/customer-specific behaviour
**How to verify:** Search the diff for literal URLs, provider names in conditionals, magic numbers that look like limits.

### EXT-04 — Depends on abstractions at seams
**Check:** At boundaries likely to change (external services, storage, messaging), does the business logic depend on an interface rather than a concrete client?
**Default severity:** SHOULD-FIX
**Applies when:** the change introduces a new external dependency
**How to verify:** Can the business logic be tested with a fake without monkey-patching a vendor SDK?

### EXT-05 — No speculative generality
**Check:** Conversely, has the author avoided building extension points for cases that aren't plausible?
**Default severity:** NITPICK
**Applies when:** the change introduces interfaces, factories or plugin mechanisms
**How to verify:** An interface with one implementation and no foreseeable second is overhead. Closed sets don't need adapters.
