# Reliability Matrix — Standard SRE Matrix (56 rows)

Use this matrix when no custom matrix file is attached by the user. If the user
provides a custom matrix (CSV, spreadsheet, or markdown), **use that instead** and
treat this file as a fallback.

Evaluate **every row** below. Do not skip rows.

| Code | Pillar | Parameter | Tier | Condition of Satisfaction |
| ---- | ------ | --------- | ---- | ------------------------- |
| OBS-01 | Observability | Health Endpoints | P1 | Service exposes a standardized unauthenticated health endpoint for load balancers and uptime checks. |
| OBS-02 | Observability | Structured Logging | P1 | Application uses structured domain-separated logging with rotation for troubleshooting. |
| OBS-03 | Observability | Centralized APM and Metrics | P1 | Production observability integrates APM metrics and alerting (Sentry, Datadog, Prometheus, etc.). |
| OBS-04 | Observability | LLM Trace Logging | P2 | LLM invocations are traceable for audit and incident response without leaking prompts in production. |
| OBS-05 | Observability | Alerting and Runbooks | P1 | Operational alerts are defined and linked to runbooks for common failure modes. |
| SEC-01 | Security | Secrets Management | P1 | Secrets are externalized; no credentials committed to source control. |
| SEC-02 | Security | Authentication and Authorization | P1 | Protected API routes require authentication; authorization model is documented. |
| SEC-03 | Security | CSRF and CORS Configuration | P1 | CSRF and CORS are correctly configured for production origins. |
| SEC-04 | Security | NL2SQL SQL Safety | P1 | Generated SQL is validated and restricted from accessing unauthorized tables. |
| SEC-05 | Security | Dependency Vulnerability Scanning | P2 | Dependencies are scanned automatically in CI for known vulnerabilities. |
| SEC-06 | Security | HTTPS and Transport Security | P1 | Production requires HTTPS for cookies and external API calls. |
| SEC-07 | Security | Host Header Hardening | P1 | ALLOWED_HOSTS restricts valid Host headers in production. |
| RES-01 | Resilience | Graceful Degradation | P2 | External dependency failures degrade gracefully without total outage. |
| RES-02 | Resilience | Retry and Backoff | P2 | Transient failures use retries with backoff for external calls and async tasks. |
| RES-03 | Resilience | Circuit Breakers | P3 | Circuit breaker pattern protects against cascading external service failures. |
| RES-04 | Resilience | Idempotent Background Jobs | P2 | Scheduled and long-running jobs are idempotent or deduplicated. |
| DEP-01 | Deployment | Production Application Server | P1 | Production uses a production-grade WSGI/ASGI server, not Django runserver. |
| DEP-02 | Deployment | Container and Orchestration Definition | P1 | Deployment artifacts (Docker, Compose, or K8s) are version-controlled and documented. |
| DEP-03 | Deployment | Automated Database Migrations | P1 | Database migrations run automatically on deploy with failure blocking rollout. |
| DEP-04 | Deployment | Rollback Capability | P1 | Documented and tested rollback procedure exists for failed deployments. |
| TST-01 | Testing | Automated Unit Tests | P2 | Comprehensive automated unit tests cover critical business logic. |
| TST-02 | Testing | CI Test Gate | P1 | Tests must pass before merge or deployment. |
| TST-03 | Testing | Integration and E2E Tests | P2 | Integration or E2E tests validate cross-service flows. |
| TST-04 | Testing | LLM Evaluation Framework | P2 | NL2SQL and agent workflows have offline evaluation with regression detection. |
| DOC-01 | Documentation | Architecture Documentation | P2 | Current architecture is documented with diagrams and decisions. |
| DOC-02 | Documentation | AI-Native Service Wiki | P2 | Complete service wiki exists for onboarding and AI agent memory. |
| DOC-03 | Documentation | API Documentation | P2 | APIs are documented via OpenAPI/Swagger and kept current. |
| DOC-04 | Documentation | Operational Runbooks | P2 | Runbooks exist for troubleshooting, deployment, and background job recovery. |
| CFG-01 | Configuration | Environment-Based Configuration | P1 | Configuration is externalized to environment variables, not code. |
| CFG-02 | Configuration | Configuration Template | P2 | Committed example.env or template documents all required variables. |
| CFG-03 | Configuration | Production Settings Separation | P1 | Dedicated production settings module separates dev and prod behavior. |
| DAT-01 | Data Reliability | Schema Version Control | P1 | Database schema changes are version-controlled and reproducible via migrations. |
| DAT-02 | Data Reliability | Backup and Restore | P1 | Backup strategy with tested restore procedure is documented. |
| DAT-03 | Data Reliability | Data Integrity Validation | P2 | Upload and processing pipelines validate data integrity (duplicates, IDs, constraints). |
| PRF-01 | Performance | Caching Strategy | P2 | Redis caching reduces database load for hot paths. |
| PRF-02 | Performance | Async Workload Offloading | P1 | CPU/IO intensive work is offloaded to background workers. |
| PRF-03 | Performance | Concurrency Controls | P2 | Background job concurrency is bounded to prevent resource exhaustion. |
| OPS-01 | Operations | Operational CLI Tools | P2 | Management commands exist for production recovery scenarios. |
| OPS-02 | Operations | Processing Status APIs | P2 | Operators can query pipeline status via API without DB access. |
| OPS-03 | Operations | Log Retention Policy | P2 | Log rotation and retention policy is defined and enforced. |
| CICD-01 | CI/CD | Automated Deployment Pipeline | P1 | CI/CD automatically deploys on configured branch pushes. |
| CICD-02 | CI/CD | Quality Gates in Pipeline | P1 | Lint and tests run in CI before deployment. |
| CICD-03 | CI/CD | Immutable Action References | P2 | GitHub Actions use pinned versions, not floating tags. |
| DR-01 | Disaster Recovery | DR Runbook | P1 | Formal disaster recovery runbook covers major failure scenarios. |
| DR-02 | Disaster Recovery | RPO and RTO Targets | P1 | Recovery Point and Time Objectives are defined and communicated. |
| AIR-01 | AI/LLM Reliability | Prompt Management | P2 | LLM prompts are versioned, managed, and reviewable. |
| AIR-02 | AI/LLM Reliability | Generated SQL Safety | P1 | NL2SQL outputs are validated before execution against allowlists. |
| AIR-03 | AI/LLM Reliability | LLM Failure Handling | P2 | LLM timeouts, rate limits, and errors are handled with user-visible feedback. |
| AIR-04 | AI/LLM Reliability | Production Inference Guards | P2 | Production mode prevents logging of sensitive prompts and system instructions. |
| ASY-01 | Async Processing | Worker Deployment | P1 | Celery workers are deployed separately from the web process. |
| ASY-02 | Async Processing | Distributed Job Locking | P2 | Long-running scheduled jobs use locking to prevent duplicate execution. |
| ASY-03 | Async Processing | Scheduled Task Reliability | P2 | Celery Beat schedule is defined for critical cron workloads. |
| ASY-04 | Async Processing | Operational Pause and Resume | P2 | Operators can pause and resume long pipelines without data loss. |
| API-01 | API Reliability | OpenAPI Specification | P2 | Machine-readable API contract is published and accessible. |
| API-02 | API Reliability | Consistent Error Responses | P2 | APIs return consistent structured error envelopes. |
| API-03 | API Reliability | API Rate Limiting | P3 | Rate limiting protects APIs from abuse and overload. |
