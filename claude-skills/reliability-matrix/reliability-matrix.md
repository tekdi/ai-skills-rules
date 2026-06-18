# Reliability Matrix — EPIC Tekdi Reliability Matrix (67 rows)

Official matrix source: `EPIC_Tekdi_Reliability_Matrix - Reliability Matrix.csv`

Use this matrix for all reliability assessments unless the user attaches a different
matrix file. If a custom matrix is provided, **use that instead**.

Evaluate **every row** below. Do not skip rows. Section headers (e.g. "CODE QUALITY &
ARCHITECTURE") are grouping labels only — they are not scored.

| Code | Tier | Pillar | Parameter | Condition of Satisfaction |
| ---- | ---- | ------ | --------- | ------------------------- |
| CQ1 | P1 | Code Quality & Architecture | Code style & linting enforced | Linter config committed to repo. CI fails on lint errors. Style guide linked in README. |
| CQ2 | P2 | Code Quality & Architecture | Modularization & separation of concerns | No god classes/functions. A new developer can identify entry point and core modules in under 15 minutes. |
| CQ3 | P1 | Code Quality & Architecture | Dependency inventory & versioning | All dependencies in manifest with pinned/semver versions. No undocumented transitive dependencies. |
| CQ4 | P2 | Code Quality & Architecture | Dead code eliminated | No orphaned files, commented-out blocks, or unused imports. Enforced via linter rule or documented manual review. |
| CQ5 | P1 | Code Quality & Architecture | No hardcoded secrets or credentials | Secret scanning run in CI. Zero findings. Any prior exposure documented and rotated. |
| CQ6 | P1 | Code Quality & Architecture | Config externalized from codebase | All env-specific config via env vars or uncommitted config files. Fresh deploy requires only setting env vars, no code changes. |
| CQ7 | P2 | Code Quality & Architecture | Technical debt register maintained | TECH_DEBT.md listing known items with severity (H/M/L) and remediation note for each. |
| OB1 | P1 | Observability & Alerting | Structured logging (JSON, consistent fields) | Every log line is JSON with: timestamp, level, service, trace_id, message. Verified by log sample. No plain-text logs in production. |
| OB2 | P2 | Observability & Alerting | Log levels enforced correctly | DEBUG/INFO/WARN/ERROR/FATAL used per a documented logging guide. No debug verbosity in production INFO logs. |
| OB3 | P3 | Observability & Alerting | Distributed tracing with correlation IDs | Requests traceable end-to-end with trace_id across service calls. Viewable in a tracing tool (Jaeger, Zipkin, etc.). |
| OB4 | P3 | Observability & Alerting | Metrics instrumented (latency, error rate, throughput) | p50/p95 latency, error rate %, and throughput per endpoint emitted as metrics and visible in a tool. |
| OB5 | P1 | Observability & Alerting | Health check & readiness endpoints | /health and /ready exist, documented, return correct status, and are used in deployment config. |
| OB6 | P1 | Observability & Alerting | Alerting rules with runbooks | Minimum 3 alerts: error rate spike, p95 latency breach, service down. Each links to a runbook with first-response steps. |
| OB7 | P2 | Observability & Alerting | Operational dashboard exists | One dashboard per service covering key metrics. Accessible to EPIC team without Tekdi involvement. |
| OB8 | P3 | Observability & Alerting | Log retention policy defined | Retention duration documented, configured, compliant with EPIC data policy. Access control documented. |
| OB9 | P3 | Observability & Alerting | Learning loop: logs → product backlog | Recurring process (automated or manual) that surfaces recurring log errors into backlog as tagged items. |
| EH1 | P1 | Error Handling & Resilience | Error taxonomy documented | All error types classified, named (error_code), listed in docs with cause and resolution. |
| EH2 | P1 | Error Handling & Resilience | User-facing errors are meaningful & actionable | All API errors follow schema: {error_code, message, details, trace_id}. No stack traces to consumers. Verified by automated test. |
| EH3 | P1 | Error Handling & Resilience | Internal errors never leak to consumers | Error boundary implemented. Exceptions mapped to consumer-safe responses. Automated test confirms no stack trace leak. |
| EH4 | P2 | Error Handling & Resilience | Retry logic with backoff | Idempotent ops retry with exponential backoff. Non-idempotent ops protected from unintended retries. |
| EH5 | P2 | Error Handling & Resilience | Circuit breaker / fallback on downstream failures | Downstream calls wrapped with circuit breaker. Fallback behavior defined and tested. No cascading failure in test scenario. |
| EH6 | P1 | Error Handling & Resilience | All outbound calls have explicit timeouts | Every external HTTP/DB/queue call has configured timeout. Timeouts documented. Behavior on timeout tested. |
| EH7 | P2 | Error Handling & Resilience | Dead letter / failure queue for async ops | Failed async operations routed to DLQ. DLQ monitored. Reprocess procedure documented. |
| EH8 | P3 | Error Handling & Resilience | Graceful degradation under partial failure | Behavior documented for each failure mode. Degraded mode tested in staging. Users notified clearly when degraded. |
| TE1 | P1 | Testing | Unit test coverage threshold enforced in CI | Coverage report in CI. Threshold ≥70%. Build fails below. Coverage report accessible. |
| TE2 | P1 | Testing | Integration test suite exists | Tests covering cross-module and cross-service interactions. Runs in CI on PR merge. Pass rate visible. |
| TE3 | P1 | Testing | Contract tests (API consumer-driven) | API contract verified against consumer expectations. Breaking changes caught before merge. |
| TE4 | P2 | Testing | End-to-end tests for critical journeys | Top 3 critical user journeys have E2E tests. Run in staging environment. Results tracked. |
| TE5 | P1 | Testing | Test data seed scripts available | Seed script in repo. Runs against blank DB to populate realistic data for all major scenarios. Documented in README. |
| TE6 | P2 | Testing | Edge case & negative path testing | Boundary values, invalid inputs, known failure modes have corresponding tests. Documented in test plan. |
| TE7 | P3 | Testing | Performance / load tests with baselines | Baseline throughput and latency at expected and 2x peak load documented. Re-run on major releases. |
| TE8 | P2 | Testing | Regression tests for all prior bugs | Every resolved bug has a regression test tagged with issue reference. Pass rate 100%. |
| TE9 | P3 | Testing | Test environment mirrors production | Config differences from production listed and justified. Parity reviewed quarterly. |
| CD1 | P1 | CI/CD & Deployment | Git branching strategy defined & enforced | BRANCHING.md in repo. Naming convention documented. CI/hook enforces it. No direct pushes to main. |
| CD2 | P1 | CI/CD & Deployment | PR / code review process documented | PR template exists. Minimum approvers defined. Review checklist in template. |
| CD3 | P1 | CI/CD & Deployment | CI pipeline: build, lint, test on every PR | CI runs on every PR. No PR merged with failing build, lint, or tests. Pipeline config in repo. |
| CD4 | P2 | CI/CD & Deployment | CD pipeline: automated deploy to staging | Merge to main triggers automated staging deploy. Deploy log accessible. No manual steps required. |
| CD5 | P1 | CI/CD & Deployment | Environment promotion flow documented | Path dev > staging > production documented with approval gates and named approvers. |
| CD6 | P1 | CI/CD & Deployment | Rollback procedure documented & tested | ROLLBACK.md exists. A rollback executed in staging at least once with outcome documented. |
| CD7 | P2 | CI/CD & Deployment | Structured changelog per release | CHANGELOG.md maintained. Each release has: date, version, what changed, breaking changes flagged. |
| CD8 | P3 | CI/CD & Deployment | Feature flags for controlled rollout | Feature flag system exists. New high-risk features gated behind flags. Kill-switch tested. |
| CD9 | P3 | CI/CD & Deployment | Infrastructure as Code | All infra defined in code, version-controlled. Can redeploy from scratch using IaC alone. |
| AP1 | P1 | API & Interface Contracts | OpenAPI / AsyncAPI spec committed to repo | Spec passes validation. Importable into Postman with zero manual edits. Kept in sync via CI check. |
| AP2 | P1 | API & Interface Contracts | API versioning strategy defined | Versioning scheme documented. Breaking changes require new version. Deprecation policy and timeline published. |
| AP3 | P1 | API & Interface Contracts | Auth mechanism documented & consistently implemented | Auth method documented. All endpoints consistently protected. Exceptions listed with justification. |
| AP4 | P2 | API & Interface Contracts | Rate limiting & throttling enforced | Limits defined per consumer/tier. Documented in API reference. 429 response includes Retry-After header. |
| AP5 | P1 | API & Interface Contracts | Input validation at API boundary | All inputs validated. Malformed inputs return structured 400 errors. Validation rules documented and tested. |
| AP6 | P2 | API & Interface Contracts | API changelog maintained | History of API changes tied to version numbers. Breaking vs non-breaking changes labelled. |
| AP7 | P3 | API & Interface Contracts | Sandbox / mock environment for consumers | Mock server or sandbox available and documented. Consumers can test without touching production data. |
| AP8 | P1 | API & Interface Contracts | SLA explicitly stated | Response time, availability %, and data freshness SLAs documented per endpoint. Measurement method defined. |
| DO1 | P1 | Documentation (Stripe-grade) | Service overview & rationale | Purpose, problem solved, key architectural decisions, trade-offs. Readable in under 10 minutes by a new engineer. |
| DO2 | P1 | Documentation (Stripe-grade) | Architecture diagram (current state) | Data flows, service boundaries, external dependencies, key components. Updated with each release. |
| DO3 | P2 | Documentation (Stripe-grade) | Data model documented | All entities, fields, types, relationships, constraints, and indexes documented. Includes ER diagram or equivalent. |
| DO4 | P1 | Documentation (Stripe-grade) | API reference (Stripe-grade) | Every endpoint: method, path, all params, request example, success + error response examples, all error codes. |
| DO5 | P1 | Documentation (Stripe-grade) | Integration guide for new developers | Step-by-step guide. A developer with no prior context makes a successful API call in under 30 min following the guide alone. |
| DO6 | P1 | Documentation (Stripe-grade) | Operational runbook | Covers: deploy, restart, scale, monitor, debug. Includes known failure modes and first-response steps. |
| DO7 | P2 | Documentation (Stripe-grade) | Known issues & limitations documented | KNOWN_ISSUES.md: description, impact, workaround, and status per item. |
| DO8 | P2 | Documentation (Stripe-grade) | Changelog / version history complete | Full release history in consistent format across all services. |
| DO9 | P1 | Documentation (Stripe-grade) | NFRs formally documented | Performance targets, scalability limits, availability SLAs, security requirements with measurable thresholds. |
| DO10 | P2 | Documentation (Stripe-grade) | Glossary of domain terms | Domain-specific terms, acronyms, EPIC-specific concepts defined. Linked from main docs. |
| DO11 | P1 | Documentation (Stripe-grade) | External validation of documentation | A developer with zero prior context reads docs and makes a successful API call in under 30 min. Time recorded. Feedback incorporated. |
| SC1 | P2 | Security & Compliance | Secrets in vault / secrets manager | All credentials in secrets manager. No secrets in committed files or env var files. |
| SC2 | P2 | Security & Compliance | Least-privilege access control documented | Roles and permissions documented. Each service account has minimum required permissions. |
| SC3 | P3 | Security & Compliance | Data classification applied | Sensitive data fields identified, classified, handling rules per classification documented. |
| SC4 | P1 | Security & Compliance | Dependency vulnerability scanning in CI | Dependabot or equivalent enabled. No HIGH/CRITICAL CVEs unresolved more than 7 days. Report visible in repo. |
| SC5 | P2 | Security & Compliance | Audit trail for state changes | Significant state changes logged with: actor, timestamp, action, before/after. Logs immutable and retained. |
| SC6 | P1 | Security & Compliance | Data compliance verified | Data handling reviewed against applicable regulations. Legal sign-off documented per EPIC legal team requirement. |

## Pillar Summary

| Pillar | Codes | Row count |
| ------ | ----- | --------- |
| Code Quality & Architecture | CQ1–CQ7 | 7 |
| Observability & Alerting | OB1–OB9 | 9 |
| Error Handling & Resilience | EH1–EH8 | 8 |
| Testing | TE1–TE9 | 9 |
| CI/CD & Deployment | CD1–CD9 | 9 |
| API & Interface Contracts | AP1–AP8 | 8 |
| Documentation (Stripe-grade) | DO1–DO11 | 11 |
| Security & Compliance | SC1–SC6 | 6 |
| **Total** | | **67** |

## Tier Summary

| Tier | Row count |
| ---- | --------- |
| P1 | 34 |
| P2 | 22 |
| P3 | 11 |
