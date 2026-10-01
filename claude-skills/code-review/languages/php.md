# PHP

**Inherits:** universal checklist → this file. Applies to plain PHP, Laravel, Symfony, and Joomla/WordPress extensions.

### PHP-01 — Strict types and type declarations
**Refines:** ERR-05
**Check:** Do new files declare `declare(strict_types=1);`, and do new functions declare parameter, return and property types?
**Default severity:** SHOULD-FIX
**Applies when:** the change adds PHP files or functions (and the project's minimum PHP version supports it)
**How to verify:** Untyped parameters on new functions fail. Legacy CMS extensions may be exempt — note it as N/A with the reason.

### PHP-02 — No error suppression
**Refines:** ERR-03
**Check:** Is the `@` error-suppression operator absent?
**Default severity:** SHOULD-FIX
**Applies when:** always
**How to verify:** Search the diff for `@$`, `@file_`, `@fopen`, etc.

### PHP-03 — Return values of fallible functions are checked
**Refines:** ERR-01, ERR-04
**Check:** Are `false`/`null` returns from functions like `file_get_contents`, `json_decode`, `strpos`, `fopen`, `curl_exec`, `preg_match` checked before use — with strict comparison (`=== false`)?
**Default severity:** BLOCKER
**Applies when:** the change calls built-in functions that signal failure via return value
**How to verify:** `if (strpos($s, 'x'))` fails when the match is at position 0. `json_decode` without `JSON_THROW_ON_ERROR` or a `json_last_error()` check fails.

### PHP-04 — Strict comparison
**Check:** Are `===` / `!==` used, and `in_array` / `array_search` called with `strict: true`?
**Default severity:** SHOULD-FIX
**Applies when:** always
**How to verify:** Loose comparison (`"abc" == 0` behaviour differs across PHP versions) causes auth and validation bugs.

### PHP-05 — Queries use parameter binding
**Refines:** SEC-02
**Check:** Are all queries built with prepared statements / the framework's query builder with bound parameters — never string interpolation of variables?
**Default severity:** BLOCKER
**Applies when:** the change builds queries
**How to verify:** `"SELECT ... WHERE id = $id"`, `DB::raw("... $input")`, `whereRaw` with interpolation fail. Joomla: `$db->quote()` / `bind()`; WordPress: `$wpdb->prepare()`.

### PHP-06 — Output is escaped for its context
**Refines:** SEC-02
**Check:** Is every variable rendered into HTML escaped (`htmlspecialchars`, Blade `{{ }}`, Twig autoescape, `esc_html`/`esc_attr`), with raw output (`{!! !!}`, `|raw`) only for trusted, sanitised content?
**Default severity:** BLOCKER
**Applies when:** the change renders output
**How to verify:** `echo $var` in a template fails.

### PHP-07 — Request input goes through the framework
**Refines:** SEC-01
**Check:** Is input read via the framework's request/validation layer (Laravel `FormRequest` / `$request->validate`, Symfony forms/validator, Joomla `Input` filters) rather than raw `$_GET`, `$_POST`, `$_REQUEST`?
**Default severity:** SHOULD-FIX
**Applies when:** the change reads request input
**How to verify:** Direct superglobal access fails.

### PHP-08 — Mass assignment is constrained
**Refines:** SEC-05
**Check:** Do Eloquent/Doctrine models define `$fillable` (or equivalent), and are `create($request->all())` patterns avoided?
**Default severity:** BLOCKER
**Applies when:** the change creates or updates models from request data
**How to verify:** `$guarded = []` or `->fill($request->all())` fails.

### PHP-09 — Eloquent / ORM avoids N+1
**Refines:** OPS-09
**Check:** Are relations eager-loaded (`with()`, `load()`) before being accessed in loops, and are large sets processed with `chunk()` / `lazy()` / `cursor()`?
**Default severity:** BLOCKER over unbounded data
**Applies when:** the change uses an ORM
**How to verify:** `foreach ($orders as $o) { $o->customer->name }` without `with('customer')` fails. `Model::all()` on growing tables fails.

### PHP-10 — Config and secrets via env/config, not literals
**Refines:** OPS-05, OPS-06
**Check:** Are credentials and environment values read via `config()` (backed by `.env`) — and is `env()` called only inside config files, not application code (it returns null once config is cached)?
**Default severity:** SHOULD-FIX (BLOCKER for secrets)
**Applies when:** the change reads configuration
**How to verify:** `env('X')` in a controller or service fails in Laravel.

### PHP-11 — Debug output removed
**Refines:** OPS-04
**Check:** Are `var_dump`, `print_r`, `dd`, `dump`, `die`, `exit` debugging calls absent?
**Default severity:** SHOULD-FIX
**Applies when:** always
**How to verify:** Search the diff.
