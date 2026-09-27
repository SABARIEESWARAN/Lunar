# LUNAR Technical Debt Model

LUNAR treats technical debt as **evidence-backed maintenance work**, not as a generic code-quality score.

## What LUNAR currently detects

| Category | Examples | Evidence source |
|---|---|---|
| Complexity | Large functions, high cyclomatic complexity | Python AST / scanner metrics |
| Duplication | Repeated validation or similar code | Code scanner |
| Security | Hard-coded credentials and unsafe patterns | Security scanner |
| Dependency | Dependency-version concerns | Dependency scanner |
| Testing | Modules without corresponding tests | Test/documentation scanner |
| Documentation | Missing public-function docstrings / README gaps | Test/documentation scanner |
| Maintenance | TODO / FIXME / HACK markers | Code scanner |
| Error handling | Silent exception handling | Code scanner |

## Priority score

LUNAR uses a project-specific relative priority score:

```text
priority_score = severity_weight × risk_weight × confidence × category_impact
```

This score is **not an industry standard**. It is used only to rank findings inside a LUNAR analysis.

The weights are documented in `backend/app/scoring.py` so the result is transparent and reproducible.

## Technical-debt effort

Each finding may include `estimated_effort_min`. LUNAR reports the sum as estimated maintenance effort.

This is an estimate, not a measurement of a company's actual engineering cost.

## Health score

LUNAR derives a 0–100 health indicator from finding density and weighted priority. It is a LUNAR-specific project metric and should only be compared between runs using the same methodology.

## Repair policy

The repair service deliberately limits automatic changes to lower-risk, well-defined transformations. Examples include:

- replacing hard-coded credentials with environment-variable references;
- handling TODO/FIXME markers in a tracked way;
- adding documentation where the repair is deterministic.

Complex refactors are reported as recommendations rather than silently rewritten.

## Verification policy

A repair is not considered successful because an AI model says it worked. Approved repairs are verified using the repository's available tests, build/static checks, and before/after metrics.

## Demo repository

`demo-repo/` is intentionally imperfect. It contains synthetic hospital-management code designed to exercise LUNAR's detectors. It contains no real patient data.

Known seeded examples include:

- hard-coded credentials;
- high-complexity and large functions;
- maintenance markers;
- missing test coverage;
- dependency concerns;
- weak error handling;
- documentation gaps.

Do not clean these findings from `demo-repo/` before the main demonstration; they are the test fixtures for the product.
