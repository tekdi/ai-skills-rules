# Java

**Inherits:** universal checklist → this file.

### JAVA-01 — Checked exceptions are not swallowed or blindly wrapped
**Refines:** ERR-02, ERR-03
**Check:** Are checked exceptions handled meaningfully, or wrapped with the cause preserved (`new ServiceException("msg", e)`), never caught and ignored?
**Default severity:** BLOCKER
**Applies when:** the change catches exceptions
**How to verify:** `catch (Exception e) { e.printStackTrace(); }` and `catch (IOException e) {}` fail. Wrapping without passing `e` as cause fails.

### JAVA-02 — No catching `Throwable` / `Error`
**Refines:** ERR-02
**Check:** Is `Throwable` or `Error` (e.g. `OutOfMemoryError`) never caught outside a top-level framework boundary?
**Default severity:** SHOULD-FIX
**Applies when:** the change has catch blocks
**How to verify:** Search for `catch (Throwable`.

### JAVA-03 — Resources use try-with-resources
**Refines:** OPS-12
**Check:** Are `AutoCloseable` resources (streams, connections, statements, readers) opened in try-with-resources?
**Default severity:** SHOULD-FIX
**Applies when:** the change opens resources outside a framework-managed scope
**How to verify:** Manual `close()` in `finally`, or no close at all, fails.

### JAVA-04 — `equals` and `hashCode` are consistent
**Check:** If a class overrides `equals`, does it override `hashCode` over the same fields (or use a `record`)?
**Default severity:** BLOCKER
**Applies when:** the change overrides `equals` or `hashCode`, or uses a class as a map key / set element
**How to verify:** One without the other breaks `HashMap` and `HashSet` silently.

### JAVA-05 — `Optional` used correctly
**Refines:** ERR-05
**Check:** Is `Optional` used as a return type for maybe-absent values — not as a field or parameter type — and never unwrapped with bare `.get()` without a presence check?
**Default severity:** SHOULD-FIX
**Applies when:** the change uses `Optional` or returns possibly-null values
**How to verify:** `.get()` without `isPresent()` fails; prefer `orElseThrow`, `map`, `orElse`. Methods returning `null` where `Optional` would clarify intent are NITPICK.

### JAVA-06 — Streams vs loops chosen for clarity
**Refines:** NAM-01, SIZ-01
**Check:** Are streams used where they read clearly, and loops used where the stream would need side effects, checked exceptions, or deeply nested lambdas?
**Default severity:** NITPICK
**Applies when:** the change uses streams
**How to verify:** Streams with `forEach` mutating external state, or lambdas longer than ~5 lines, fail — extract a method or use a loop.

### JAVA-07 — Immutability by default
**Check:** Are fields `final` where possible, DTOs/value objects `record`s (Java 16+) or immutable, and collections returned as unmodifiable views?
**Default severity:** NITPICK
**Applies when:** the change adds classes
**How to verify:** Setters on value objects and returning internal mutable lists fail.

### JAVA-08 — Thread-safety of shared state
**Check:** Is shared mutable state in singletons (Spring beans are singletons by default) either avoided or properly synchronised?
**Default severity:** BLOCKER
**Applies when:** the change adds fields to Spring beans or other shared objects
**How to verify:** A non-final instance field written during request handling in a `@Service` / `@Component` fails.

### JAVA-09 — Spring transactions are scoped correctly
**Refines:** ERR-07
**Check:** Is `@Transactional` on public methods called from outside the bean (self-invocation bypasses the proxy), with `readOnly = true` for reads and explicit rollback rules for checked exceptions?
**Default severity:** SHOULD-FIX
**Applies when:** the change uses `@Transactional`
**How to verify:** `@Transactional` on a private method, or a method calling its own `@Transactional` method, fails silently.

### JAVA-10 — JPA avoids N+1 and unbounded fetches
**Refines:** OPS-09, OPS-10
**Check:** Are associations `LAZY` by default, fetched with `JOIN FETCH` / entity graphs where needed, and are list queries paginated (`Pageable`)?
**Default severity:** BLOCKER over unbounded data
**Applies when:** the change uses JPA/Hibernate
**How to verify:** Accessing a lazy collection inside a loop over entities fails. `findAll()` without paging on a growing table fails.

### JAVA-11 — Logging uses the framework with placeholders
**Refines:** OPS-04
**Check:** Is SLF4J (or the project's logger) used with `{}` placeholders, not string concatenation or `System.out`?
**Default severity:** NITPICK
**Applies when:** the change adds logging
**How to verify:** `log.info("user " + id)` and `System.out.println` fail.
