# Python

**Inherits:** universal checklist → this file.

### PY-01 — No bare or over-broad `except`
**Refines:** ERR-02, ERR-03
**Check:** Are exceptions caught by specific type, with no bare `except:` and no `except Exception: pass`?
**Default severity:** BLOCKER
**Applies when:** the change has exception handling
**How to verify:** Bare `except:` also catches `KeyboardInterrupt` and `SystemExit`. `except Exception` is acceptable only at a top-level boundary that logs and re-raises or returns a controlled error.

### PY-02 — Exceptions are chained
**Refines:** ERR-06
**Check:** When re-raising as a different type, is `raise NewError(...) from e` used so the original cause is preserved?
**Default severity:** SHOULD-FIX
**Applies when:** the change catches and raises a different exception
**How to verify:** `raise ValueError("bad")` inside an `except` block without `from e` loses the root cause.

### PY-03 — No mutable default arguments
**Check:** Are default argument values immutable (`None`, then create inside the function)?
**Default severity:** BLOCKER
**Applies when:** the change defines functions with defaults
**How to verify:** `def f(items=[])` or `def f(opts={})` fails — the default is shared across calls.

### PY-04 — Resources use context managers
**Refines:** OPS-12
**Check:** Are files, locks, DB connections/sessions and HTTP sessions opened with `with` (or `async with`)?
**Default severity:** SHOULD-FIX
**Applies when:** the change opens resources
**How to verify:** `f = open(...)` without `with` fails.

### PY-05 — Type hints on public functions
**Check:** Do new public functions and methods have type hints for parameters and return values, and does the project's type checker (mypy/pyright) pass?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds public functions
**How to verify:** Missing hints on new public API fail; `Any` needs justification (same spirit as TS-01).

### PY-06 — Data classes for structured data
**Refines:** NAM-01, ERR-05
**Check:** Is structured data passed as `dataclass`, Pydantic model, `TypedDict` or `NamedTuple`, rather than loose dicts with string keys?
**Default severity:** NITPICK
**Applies when:** the change passes structured data between functions
**How to verify:** `data["user"]["address"]["city"]` chains across function boundaries suggest a missing model.

### PY-07 — Async code doesn't block the loop
**Refines:** OPS-09
**Check:** In `async` code (FastAPI, aiohttp), are blocking calls (`requests`, sync DB drivers, `time.sleep`, heavy CPU) avoided or offloaded to a thread/executor?
**Default severity:** BLOCKER in request handlers
**Applies when:** the change adds `async def` code
**How to verify:** `requests.get` inside `async def` fails; use `httpx.AsyncClient`.

### PY-08 — `requests`/`httpx` calls set timeouts
**Refines:** OPS-11
**Check:** Does every HTTP call pass an explicit `timeout`?
**Default severity:** SHOULD-FIX
**Applies when:** the change makes HTTP calls
**How to verify:** `requests` has no default timeout — it can hang forever.

### PY-09 — ORM queries avoid N+1
**Refines:** OPS-09
**Check:** With Django ORM / SQLAlchemy, are related objects loaded with `select_related` / `prefetch_related` / `joinedload` / `selectinload` when accessed in loops?
**Default severity:** BLOCKER over unbounded data
**Applies when:** the change iterates over querysets and accesses relations
**How to verify:** `for order in orders: order.customer.name` without prefetching fails.

### PY-10 — No logging via `print`, and lazy log formatting
**Refines:** OPS-04
**Check:** Is `logging` (or the project's logger) used instead of `print`, with `logger.info("x=%s", x)` style rather than f-strings in hot paths?
**Default severity:** NITPICK
**Applies when:** the change adds logging
**How to verify:** `print(` in application code fails.

### PY-11 — Dependencies pinned in the project's manifest
**Refines:** SEC-07
**Check:** Are new packages added to the project's dependency file (`pyproject.toml`, `requirements.txt`, lockfile) with appropriate pinning?
**Default severity:** SHOULD-FIX
**Applies when:** the change imports a new third-party package
**How to verify:** Imports with no matching manifest entry fail.
