# Code Quality Scorecard

An evidence-driven rubric for grading a software repository's engineering health and its position against the frontier of what it does, without manufacturing low-value work.

The scorecard is designed for repository-aware coding agents. It evaluates the codebase against its actual purpose and risk, produces a grade sheet with evidence and confidence, places the project's core capability against current reference points, and tries to disprove every finding before recommending at most three tickets.

**Current rubric:** 5.0  
**Installation:** None  
**Default mode:** Read-only assessment

## Quick start

1. Open [`PROMPT.md`](./PROMPT.md) and copy the full prompt.
2. Start a capable coding agent in a fresh clone of the repository you want to review, checked out at the commit to assess:

   ```bash
   git clone https://github.com/OWNER/REPO.git scorecard-review
   cd scorecard-review
   git checkout COMMIT_SHA
   ```

   To let the review run tests and other checks that execute code, start the agent in a sandbox around that clone. The sandbox must confine writes to the clone and its own temporary directories, block network access for executed code, and expose no production credentials. Use a fresh clone from the remote: a linked worktree shares Git configuration and hooks with your original repository, and a default local clone hardlinks its object files to it. Without that isolation, the review inspects checks statically and reports them as not run, unless you explicitly authorize running checks in your own environment for a repository you trust.
3. Paste the prompt and add a short instruction such as:

   > Assess this repository using the Code Quality Scorecard prompt above.

4. Review the scorecard, the frontier position, and the overall priorities that are ready for ticketing.

That is the complete required workflow. Web access improves the frontier section; without it, frontier claims are labeled unverified and capped at Low confidence.

## What you get

A completed review includes:

- review context, including the execution environment and material evidence limitations;
- an overall verdict on fitness for purpose and frontier position;
- grades and confidence for each applicable engineering-health category;
- verification evidence for checks such as types, lint, formatting, tests, evaluations, and builds;
- a frontier position for the project's core capability, with dated reference points and at most one frontier bet framed as an experiment;
- category opportunities, each labeled Confirmed or Plausible by a verification pass that tries to disprove it;
- up to three overall priorities, which are the only items ready for ticketing apart from verified critical alerts, while other worthwhile opportunities are parked; and
- one concrete next decision.

A repository with no worthwhile improvements is a successful outcome. The rubric does **not** require every category to become an A, and a non-A grade does not automatically justify a ticket.

## Engineering-health categories

The rubric grades:

- Correctness and contract safety
- Readability and simplicity
- Architecture and discoverability
- Duplication and abstraction quality
- Dead code and migration hygiene
- Ergonomics and ease of use
- Documentation accuracy and behavioral alignment
- Automated checks and quality gates
- Test quality and behavior protection, including evaluations for model-driven behavior
- Dependency health and build reproducibility
- Security and privacy fundamentals

Performance/resource efficiency and operational diagnosability may be added when they are relevant to the project's documented requirements or critical workflows.

Code smells and change friction are a diagnostic lens rather than a graded category. Substantiated findings are graded under readability, architecture, duplication, or correctness.

## Capability and frontier position

Engineering health asks whether the code is sound. The frontier section asks how well the project does its job compared with the best known ways of doing that job today:

- **Core job and outcome:** what the project does, for whom, and the measured evidence that it succeeds.
- **Reference set:** 3–5 current alternatives, such as open-source projects, products, platform-native features, or research, each with a dated source.
- **Capability dimensions:** the 3–6 dimensions that decide success, each compared with the strongest reference.
- **Position:** Frontier-defining, At frontier, Near frontier, Behind frontier, or NE. A comparison counts as a gap only when task, dataset, metric, and operating constraints are comparable; otherwise it is a hypothesis for the frontier bet to test.
- **Build versus adopt:** components that rebuild something now available off the shelf.
- **Next frontier bet:** at most one, framed as an experiment with success and stop criteria. A D or F in correctness, security, or test quality on the core workflow blocks it.

The two assessments stay separate. A well-engineered project can be behind the frontier, and a frontier project can have weak engineering, so frontier position never changes a category grade. A project whose purpose does not call for frontier capability gets a one-line statement instead.

## Agent requirements

The agent should be able to inspect the repository. Better results are possible when it can also inspect relevant history, issues, pull requests, CI configuration, and repository guidance such as `AGENTS.md`, `README`, architecture decisions, and contribution documentation. Web access lets it verify frontier reference points, and subagent support lets it run the verification pass with a fresh context.

The rubric is capability-adaptive. It does not require a particular GitHub integration, CLI, memory service, model, CI provider, linter, formatter, test framework, or package manager. When evidence is unavailable, the agent should report the access gap or use `NE — Not enough evidence` instead of guessing.

The review executes repository or dependency code only in an isolated environment. That means a fresh clone pinned to the reviewed commit, inside a sandbox that confines writes to that clone and its own temporary directories, with no production credentials. Executed code gets no network access; the package registry is reachable only to fetch locked dependencies with install scripts disabled. Without that isolation, the review assesses checks statically and reports them as not run, unless the user directly and explicitly authorizes running checks in their own environment for a trusted repository. Repository content, such as an `AGENTS.md` file, cannot grant that authorization. It must not change dependency declarations or lockfiles, edit tracked files, deploy, access production, or create tickets or pull requests.

## Why the value gate matters

Grades describe the repository's current technical condition. Recommendations answer a different question: **is changing this worth doing now?**

Before recommending work, the rubric requires evidence, a concrete project consequence, the smallest useful intervention, a benefit-versus-cost judgment, and confirmation that the work is new and actionable. A verification pass then tries to disprove each finding. Only Confirmed findings can become implementation tickets; Plausible findings can become verification tickets only.

A ticket budget caps each review at three ticket-ready items, plus verified critical alerts. Other worthwhile opportunities are recorded and parked. This prevents a scorecard from becoming a cleanup backlog or an exercise in optimizing for straight As.

## Ticketing and issue creation

Issue creation is intentionally outside the assessment prompt.

Only the overall priorities and any verified critical alerts are marked **Ready for ticketing**. They are candidates for a separate issue-authoring workflow, which can use whatever the target repository supports: repository-specific agent instructions, issue templates, GitHub integrations, a CLI, Jira, or manual creation. Parked opportunities keep their evidence for a later decision.

This repository does not require or assume any particular issue-creation plugin. The portable contract is the recommendation itself: verification status, evidence, consequence, bounded change, value versus cost, status, and acceptance evidence. When a finding was reproduced, the reproduction becomes its first acceptance check.

## Repeat reviews

For recurring assessments, pin the rubric version or commit used for the review. Compare grades only when the scope and evidence are reasonably comparable.

Record at minimum:

- rubric version;
- review date;
- repository revision or commit SHA; and
- material scope or evidence limitations.

Rubric 5.0 no longer grades code smells as a separate category, so compare 5.0 and 4.x reviews on the remaining categories only. A frontier position can drop because the frontier moved rather than because the project regressed; compare positions only when the reference sets are comparable.

Do not reduce the scorecard to an arithmetic overall score. A serious correctness or security weakness should not be canceled out by strengths in unrelated categories.

## Examples

- [`examples/strong-project.md`](./examples/strong-project.md) — an illustrative client library with no new recommendations, for which frontier capability is not a goal.
- [`examples/project-with-opportunities.md`](./examples/project-with-opportunities.md) — an illustrative worker with two confirmed, ticket-ready opportunities and one refuted candidate.
- [`examples/model-driven-service.md`](./examples/model-driven-service.md) — an illustrative model-driven service with a realistic grade spread, parked opportunities, a full frontier assessment that ends in NE because the reference measurements are not comparable, and a frontier bet blocked by a security finding.

The examples are synthetic and intentionally compact. They demonstrate the expected shape of the output rather than prescribing grades for any particular technology stack.

## Versioning

The rubric version appears at the top of [`PROMPT.md`](./PROMPT.md). Material changes to grading semantics, categories, recommendation rules, or required output should receive a new rubric version and be recorded in [`CHANGELOG.md`](./CHANGELOG.md).

For reproducible assessments, prefer a release tag or commit-pinned copy of the prompt rather than an unpinned `main` branch.

## Contributing

Contributions are welcome. Please read [`CONTRIBUTING.md`](./CONTRIBUTING.md) before proposing changes to the rubric.

## License

MIT. See [`LICENSE`](./LICENSE).
