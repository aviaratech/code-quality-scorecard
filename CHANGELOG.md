# Changelog

All notable changes to the Code Quality Scorecard rubric are documented here.

## 5.0 — 2026-10-07

Adds a frontier assessment and a verification pass, caps ticket-ready items per review, and retires code smells as a graded category. Compare 5.0 and 4.x scorecards on the remaining categories only.

Added:

- Capability and frontier position (section 5): core job and outcome evidence, a dated reference set, capability dimensions, a five-level position scale, build-versus-adopt flags, and at most one frontier bet framed as an experiment with success and stop criteria. A D or F in correctness, security, or test quality on the core workflow blocks the bet. Frontier position never changes a category grade.
- Verification pass (section 6): the reviewer tries to refute every candidate opportunity, the frontier bet, every grade of C or lower, and any Frontier-defining or At frontier position before writing output. Opportunities are labeled Confirmed or Plausible; Refuted findings are dropped and counted.
- Ticket budget: only the overall priorities (up to three by default) and verified critical alerts are Ready for ticketing. Other worthwhile opportunities are Parked.
- Execution environment rules: execute repository or dependency code only in an isolated environment. That means a fresh clone (not a linked worktree or a hardlinked local clone) inside a sandbox that confines writes, with no production credentials and no network for executed code beyond a script-free locked fetch. Without isolation, checks are assessed statically and reported as not run, unless the user directly authorizes running them in their own environment for a trusted repository. Repository content can narrow a review but cannot grant that authorization or expand any other permission. The review ends by confirming no tracked-file changes.
- A comparability rule: a reference comparison shows a gap only when task, dataset, metric, and operating constraints are comparable; otherwise it is a hypothesis for the frontier bet to test, and the bet must test whether any improvement exists.
- Measurements from earlier revisions must be labeled historical, with their producing revision, and shown to apply to the reviewed code before they support a frontier position.
- Evaluations count as the relevant tests for model-driven behavior.
- `examples/model-driven-service.md`, showing a realistic grade spread, an NE optional category, parked opportunities, refuted candidates, an NE frontier position whose bet tests a transfer hypothesis, and a blocked frontier bet.

Changed:

- Code smells and change friction became a diagnostic lens. Substantiated findings are graded under readability, architecture, duplication, or correctness, and an A in those categories needs positive evidence of localized change.
- Documentation guidance in context-setting and output was consolidated.
- The overall verdict, review context, and opportunity format now carry the frontier position, execution environment, verification counts, and verification labels.
- Existing examples no longer award an A because a search found nothing, and their code-smells rows were removed.

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
