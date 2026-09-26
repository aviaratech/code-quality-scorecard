# Changelog

All notable changes to the Code Quality Scorecard rubric are documented here.

## 4.0 — 2026-09-25

Initial public baseline.

Highlights:

- evidence-backed Principal/Staff-level repository assessment;
- explicit A/B/C/D/F/N/A/NE grading semantics with confidence;
- value gate separating technical condition from whether work is worth doing now;
- core categories covering correctness, readability, code smells, architecture, abstraction, migration hygiene, ergonomics, documentation, checks, tests, dependencies/builds, and security/privacy;
- optional performance/resource-efficiency and operational-diagnosability categories when justified;
- direct verification of existing checks where safe and supported;
- first-use workflow inspection for packages, applications, and runtimes;
- safeguards against detector-driven refactoring, arbitrary thresholds, dependency churn, documentation busywork, and duplicated recommendations;
- category-level opportunities plus up to three overall priorities;
- explicit read-only boundary: no implementation, ticket creation, dependency changes, deployment, or external-system mutation during assessment.
