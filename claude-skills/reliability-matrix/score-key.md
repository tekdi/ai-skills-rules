# Reliability Matrix — Score Key

Translate the score key into a **0–10 scale**. Intermediate values are allowed.

| Score | Meaning |
|-------|---------|
| **0** | Not started — no evidence found |
| **1–2** | Minimal / ad hoc |
| **3–4** | Partial implementation |
| **5–6** | Mostly implemented; significant gaps exist |
| **7** | Condition fully satisfied |
| **8** | Condition satisfied and operationalized |
| **9** | Strong implementation with automation |
| **10** | Best-practice implementation exceeding requirements |

## Scoring Guidance

The score must reflect **confidence and implementation maturity**.

| Example score | When to use |
|---------------|-------------|
| 0 | No evidence found in repository or wiki |
| 3 | Partial implementation; key pieces missing |
| 5 | Mostly implemented but significant gaps remain |
| 7 | Condition fully satisfied with evidence |
| 8 | Satisfied and operationalized (runbooks, CI enforcement, monitoring) |
| 9 | Strong implementation with automation |
| 10 | Best practice exceeding requirements |

## Maturity Bands

Use these bands in the executive summary:

| Score range | Maturity band |
|-------------|---------------|
| 0–2 | Ad Hoc |
| 3–4 | Developing |
| 5–6 | Managed |
| 7–8 | Reliable |
| 9–10 | Highly Reliable |

## Priority Model

When creating remediation tasks (score &lt; 8), assign priority using:

1. Matrix tier (P1 / P2 / P3)
2. Reliability risk
3. Production impact
4. Security impact
5. Operational impact

| Example gap | Typical priority |
|-------------|------------------|
| P1 security gap | Critical |
| P1 observability gap | High |
| P2 documentation gap | Medium |
| P3 optimization gap | Low |
