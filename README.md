# Code Quality Scorecard

An evidence-driven rubric for grading the engineering health of a software repository without manufacturing low-value cleanup work.

The scorecard is designed for repository-aware coding agents. It evaluates the codebase against its actual purpose and risk, produces a grade sheet with evidence and confidence, and identifies only improvement opportunities that clear an explicit value gate.

**Current rubric:** 4.0  
**Installation:** None  
**Default mode:** Read-only assessment

## Quick start

1. Open [`PROMPT.md`](./PROMPT.md).
2. Copy the full prompt.
3. Paste it into a capable coding agent that has access to the repository you want to review.
4. Add a short instruction such as:

   > Assess this repository using the Code Quality Scorecard prompt above.

5. Review the resulting scorecard and any evidence-backed opportunities.

That is the complete required workflow.

## What you get

A completed review includes:

- review context and material evidence limitations;
- an overall fitness-for-purpose verdict;
- grades and confidence for each applicable category;
- verification evidence for checks such as types, lint, formatting, tests, and builds;
- normally 1–3 worthwhile opportunities for each category graded B or lower, subject to the value gate;
- up to three overall priorities when action is justified; and
- one concrete next decision.

A repository with no worthwhile improvements is a successful outcome. The rubric does **not** require every category to become an A, and a non-A grade does not automatically justify a ticket.

## Core categories

The rubric assesses:

- Correctness and contract safety
- Readability and simplicity
- Code smells and change friction
- Architecture and discoverability
- Duplication and abstraction quality
- Dead code and migration hygiene
- Ergonomics and ease of use
- Documentation accuracy and behavioral alignment
- Automated checks and quality gates
- Test quality and behavior protection
- Dependency health and build reproducibility
- Security and privacy fundamentals

Performance/resource efficiency and operational diagnosability may be added when they are relevant to the project's documented requirements or critical workflows.

## Agent requirements

The agent should be able to inspect the repository. Better results are possible when it can also inspect relevant history, issues, pull requests, CI configuration, and repository guidance such as `AGENTS.md`, `README`, architecture decisions, and contribution documentation.

The rubric is capability-adaptive. It does not require a particular GitHub integration, CLI, memory service, model, CI provider, linter, formatter, test framework, or package manager. When evidence is unavailable, the agent should report the access gap or use `NE — Not enough evidence` instead of guessing.

The review may run existing non-fixing local validation commands when they are safe and already supported by the environment. It must not install or upgrade dependencies, mutate tracked files, deploy, access production to complete the review, or create tickets or pull requests.

## Why the value gate matters

Grades describe the repository's current technical condition. Recommendations answer a different question: **is changing this worth doing now?**

Before recommending work, the rubric requires evidence, a concrete project consequence, the smallest useful intervention, a benefit-versus-cost judgment, and confirmation that the work is new and actionable.

This prevents a scorecard from becoming a cleanup backlog or an exercise in optimizing for straight As.

## Ticketing and issue creation

Issue creation is intentionally outside the assessment prompt.

A recommendation marked **Ready for ticketing** is a candidate for a separate issue-authoring workflow. That workflow can use whatever the target repository supports: repository-specific agent instructions, issue templates, GitHub integrations, a CLI, Jira, or manual creation.

This repository does not require or assume any particular issue-creation plugin. The portable contract is the recommendation itself: evidence, consequence, bounded change, value versus cost, status, and acceptance evidence.

## Repeat reviews

For recurring assessments, pin the rubric version or commit used for the review. Compare grades only when the scope and evidence are reasonably comparable.

Record at minimum:

- rubric version;
- review date;
- repository revision or commit SHA; and
- material scope or evidence limitations.

Do not reduce the scorecard to an arithmetic overall score. A serious correctness or security weakness should not be canceled out by strengths in unrelated categories.

## Examples

- [`examples/strong-project.md`](./examples/strong-project.md) — an illustrative project with no new recommendations.
- [`examples/project-with-opportunities.md`](./examples/project-with-opportunities.md) — an illustrative project with a small number of evidence-backed opportunities.

The examples are synthetic and intentionally compact. They demonstrate the expected shape of the output rather than prescribing grades for any particular technology stack.

## Versioning

The rubric version appears at the top of [`PROMPT.md`](./PROMPT.md). Material changes to grading semantics, categories, recommendation rules, or required output should receive a new rubric version and be recorded in [`CHANGELOG.md`](./CHANGELOG.md).

For reproducible assessments, prefer a release tag or commit-pinned copy of the prompt rather than an unpinned `main` branch.

## Contributing

Contributions are welcome. Please read [`CONTRIBUTING.md`](./CONTRIBUTING.md) before proposing changes to the rubric.

## License

MIT. See [`LICENSE`](./LICENSE).
