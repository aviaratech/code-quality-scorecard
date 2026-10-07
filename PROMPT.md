# Value-Driven Code Quality Scorecard

Rubric version: 5.0

## Role and objective

Act as a Principal/Staff engineer reviewing this repository. Produce an evidence-backed scorecard that answers two separate questions: **how sound is the engineering** (sections 2–4), and **how close is the project to the frontier of what it does** (section 5). For **each category graded B or lower**, normally identify its **top 1–3 worthwhile improvement opportunities**, subject to the value gate below. Then select up to three **overall priorities**; they are the review's ticket budget.

The purpose is to identify worthwhile work, not to generate cleanup tasks or feature wishlists, maximize abstraction, or make every category an A. A review with no recommended changes is a successful outcome when the evidence supports it.

**Grades describe the current technical condition. Recommendations separately account for benefit, effort, risk, timing, and project priorities. A non-A grade does not automatically justify a ticket.**

This is an assessment, not an implementation task. Do not edit tracked files, apply fixes, create tickets or PRs, change dependency declarations or lockfiles, deploy, or change external systems. Disposable copies, locked installs, and temporary probes are allowed only as described in sections 1 and 6. Do not print secrets or sensitive data.

## Optional overrides

Use the defaults below without requiring clarification. Apply any supplied project purpose, critical workflows, review scope, priorities, protected paths, compatibility commitments, ticket budget, frontier reference set, or prior scorecard. Obtain these from repository documentation when available; do not invent them.

## 1. Establish context and coverage

- Read applicable repository guidance, including `AGENTS.md`, `CLAUDE.md`, `CONSTITUTION.md`, README files, and relevant architecture decisions when present. Respect project-specific priorities and protected items. Missing optional documents are not defects.
- Record the review date, branch, commit SHA, and whether local modifications affect the reviewed snapshot. If an identifier cannot be verified, mark it unavailable rather than guessing.
- Identify the project's purpose, maturity, supported environments, critical workflows, package boundaries, public interfaces, and established validation commands. Identify applicable audiences: package consumers, contributors, operators, and agents. Label assumptions and distinguish documented requirements from your interpretation.
- Review available adjacent issues, open and recently merged PRs, relevant git history, and recorded decisions. Check whether apparent debt is intentional, already tracked, being migrated, or previously rejected. Use available tooling rather than assuming a particular memory service or GitHub CLI exists. Report access gaps once.
- Default to a **repository-wide inventory with representative, risk-based inspection**. Trace critical workflows through their entrypoints, boundaries, implementation, and tests. Sample shared code and representative secondary areas; do not base a repo-wide grade on a few convenient files. If scope was supplied, stay within it; for large repositories, name the reviewed subsystems and gaps. Do not require a scope-selection conversation before producing a useful scorecard, and never imply that sampling is exhaustive.
- **Execution environment.** Prefer a disposable clone or worktree pinned to the reviewed commit; do not install into the user's working checkout. In a disposable copy you may fetch dependencies exactly as locked, such as with a frozen-lockfile install, with install lifecycle scripts disabled where the package manager supports it; never add, remove, or upgrade dependencies. Install scripts, builds, tests, and evaluations execute repository and dependency code, so run them only where isolation is established: no production credentials reachable through environment variables, credential files, or ambient cloud identities, and no network access beyond the package registry. Without that isolation, run only checks that inspection shows cannot reach external services, and report the rest as **Not run — isolation not established**. Production access, external writes, destructive tests, and migrations are out of bounds in every case. Finish by confirming that the reviewed checkout has no tracked-file changes, and report any that appear.
- Inspect scripts before running them, then run existing, non-fixing validation commands when safe and supported; disposable build/test artifacts are acceptable. If a check is unsafe or unavailable, report that limitation instead of running it. Distinguish checks executed during this review from historical results or static inspection, and report actual outcomes, failures, and environmental blockers. A passing check is evidence for what it tests, not proof of whole-project correctness.
- Trace an applicable first-use workflow: package installation/import and a minimal consumer example, or application/runtime setup, configuration, startup, validation, and troubleshooting. Use existing test fixtures and the execution environment above; do not access production. Distinguish documented instructions, inspected configuration, and directly verified behavior. Do not assume an internal workspace import proves the distributed package is usable.
- Inventory applicable checks and trace their effective scope and execution: types, lint, formatting, tests, evaluations, builds, and other existing risk-relevant safeguards. Inspect exclusions, command chaining, failure propagation, workflow triggers, and bypasses. Distinguish a defined script, an executed CI job, and a verified merge requirement; do not claim enforcement from configuration alone.
- Identify applicable documentation surfaces—README and onboarding guides, API/CLI references, examples, configuration templates, consequential comments, architecture notes, operator runbooks, agent instructions, and accessible published documentation (state access gaps)—and match each claim to its intended revision, release, and environment. Clearly labeled historical decisions or planned features are not promises about current behavior. Cross-check representative, consequential claims against implementation, configuration, relevant tests, and safe execution where available, in both directions: do documented features and guarantees hold, and are important behaviors, prerequisites, limitations, defaults, failure modes, and side effects explained for the intended audience? Record what was inspected versus executed; a plausible-looking example is not a working one.
- Investigate change-friction candidates in representative maintained code by tracing relevant callers, state transitions, and likely changes supported by current responsibilities, requirements, or history. Inspect context before accepting a detector warning or smell label. Distinguish maintained source from generated/vendor output; do not recommend hand-editing generated code to satisfy a heuristic.

## 2. Evaluate these categories

| Category | Evaluate |
|---|---|
| **Correctness and contract safety** | Important invariants, state transitions, data integrity, async behavior, error handling, cleanup, and input/output contracts. Assess runtime validation at trust boundaries and consistency between types, schemas, and behavior. |
| **Readability and simplicity** | Whether a maintainer can follow the code's intent and control flow; naming, local complexity, side effects, indirection, responsibility size, and explanations of non-obvious decisions. Reward clarity, not shortness or a particular coding style. |
| **Architecture and discoverability** | Cohesive responsibilities, dependency direction, coupling, cycles, package boundaries, public interfaces, and filesystem navigation. Compare with the project's stack and established conventions; require a concrete consequence before treating a different folder layout as a defect. |
| **Duplication and abstraction quality** | Repeated business rules, contracts, state machines, and behavior that should stay consistent; existing abstractions that create unnecessary indirection or coupling. Distinguish shared semantics from superficially similar code. Assess both under-abstraction and over-abstraction. |
| **Dead code and migration hygiene** | Unreachable or obsolete code, unused dependencies/configuration/assets, retired flags and shims, competing implementations, partially completed migrations, and legacy paths that impose real cost. Assess whether retirement preconditions are met. Versioned names alone are not debt. |
| **Ergonomics and ease of use** | For packages: installation/import, discoverable APIs, sensible defaults, configuration clarity, useful errors, accurate examples, and applicable type information. For apps/runtimes: discoverable setup, configuration, startup/shutdown, local development, validation, and troubleshooting workflows for humans and agents. Assess prerequisites, hidden state, manual steps, non-interactive execution where appropriate, and reliance on undocumented knowledge. Judge applicable consumer and contributor/operator journeys; do not require every project to support every audience. |
| **Documentation accuracy and behavioral alignment** | Whether documentation faithfully describes supported functionality and actual behavior for its intended version and audience. Assess setup instructions, API/CLI examples, configuration/defaults, guarantees, limitations, error behavior, side effects, architecture, and operational/agent guidance as applicable. Identify misleading claims, contradictory sources, obsolete instructions, and consequential omissions. Reward accurate, sufficient guidance—not document volume or exhaustive coverage of internal symbols. |
| **Automated checks and quality gates** | Whether applicable typechecking, linting, formatting checks, tests, evaluations, builds, and other justified safeguards actually cover the relevant code, execute reliably, and report failures. Assess discoverable local commands, appropriate CI execution, failure propagation, intentional versus accidental exclusions, bypasses, and signal-to-noise. Judge whether required validation is enforced at the appropriate stage; do not require every check on every edit. |
| **Test quality and behavior protection** | Whether tests meaningfully protect critical behavior, contracts, failure paths, and likely changes; appropriate integration/boundary coverage, representative assertions, and manageable test maintenance. Judge what the tests would detect, not merely whether they execute. Do not use coverage percentage or test counts as automatic grades. For model-driven behavior—prompts, agents, ranking, forecasting—evaluations against representative cases are the relevant tests: assess whether they exist, run on relevant changes, and would catch a regression. |
| **Dependency health and build reproducibility** | Dependency justification, unnecessary or conflicting dependencies, relevant support/security status when verifiable, lockfiles and supported toolchain consistency where applicable, repeatable builds, and distribution/artifact correctness. An older version is not a defect solely because a newer version exists. |
| **Security and privacy fundamentals** | Applicable trust boundaries, authorization, sensitive-data handling, exposed secrets, injection risks, unsafe defaults, and logging/telemetry privacy. This is a scoped engineering review, not a security certification. |

**Change friction is a diagnostic lens, not a graded category.** Oversized or mixed-responsibility units; deeply nested or flag-driven logic; long positional parameter lists; hidden mutable state, side effects, or call-order dependencies; inappropriate access to another module's internals; one logical change scattered across unrelated locations; unclear domain values; and speculative indirection are investigation signals, not automatic defects. Grade a substantiated finding under its most specific category—readability for local comprehension, architecture for boundaries and coupling, duplication for rules that must change together, correctness for a credible failure path—based on demonstrated maintenance burden or failure risk, not smell counts or arbitrary size thresholds.

Add **performance/resource efficiency** or **operational diagnosability** only when relevant to documented requirements, critical workflows, or concrete evidence. Explain why an optional category was included. Product capability is assessed separately in section 5; do not fold capability or frontier judgments into category grades.

**Keep the category boundaries explicit:** ergonomics concerns how easily someone can use and work with the project; documentation concerns whether its explanations and instructions are accurate and sufficient for the intended audience; automated checks concern whether safeguards run, cover the intended scope, and surface or block failures; test quality concerns which important behaviors those tests actually protect. Dependency/build health concerns the underlying dependency and build mechanics. An awkward interface can be documented accurately, and an intuitive interface can have misleading documentation. Cross-reference shared findings rather than recommending the same fix twice.

Assign each underlying finding a primary category. Give each opportunity one ID and cross-reference it from other affected categories. Do not create multiple recommendations for the same root cause. Distinct demonstrated consequences may affect multiple grades; do not multiply deductions just because a finding fits several labels.

## 3. Use a consistent grading rubric

Grade fitness for this project's actual purpose and risk, not proximity to an ideal greenfield design.

| Grade | Meaning |
|---|---|
| **A — Strong** | The reviewed surface is clear, reliable, and fit for purpose in this category, with positive supporting evidence and no material weakness identified. This does not mean perfect or exhaustively verified. |
| **B — Sound** | Generally healthy, with localized weaknesses of limited consequence. No broad remediation is indicated; a specific improvement may or may not be worthwhile. |
| **C — Meaningful weakness** | Demonstrated shortcomings materially affect maintainability, safe change, correctness, or relevant risk. Focused attention is warranted, but the value gate still determines whether to recommend work now. |
| **D — Serious weakness** | Substantial or systemic deficiencies threaten important behavior or routinely make safe maintenance difficult. Prioritization or containment is warranted. |
| **F — Critical failure** | A verified critical failure or exposure compromises an essential requirement. Use sparingly and identify the concrete consequence. |
| **N/A — Not applicable** | The category genuinely does not apply to this project or the agreed scope. State why. |
| **NE — Not enough evidence** | Available access, inspection, or validation is insufficient for a defensible grade. State the specific gap. Do not substitute an assumed A or F. |

For each assessed category, report **High / Medium / Low confidence** and briefly explain material limitations. High confidence requires representative direct evidence with appropriate corroboration; medium indicates useful but bounded evidence. When the gaps prevent a meaningful judgment, use NE rather than a speculative letter grade.

Base grades on demonstrated severity, reach, frequency where known, and relevance to critical workflows. Use metrics as investigation signals, not automatic thresholds. Do not invent measurements, risk probabilities, savings, or precise ROI.

Missing review access generally lowers confidence or produces NE; it is not evidence of bad code. A verified absence of an important engineering safeguard can itself affect its relevant grade, with a stated consequence.

Support good grades with positive evidence as well as supporting weaker grades with deficiencies. Do not award A merely because a search found nothing. For B-or-lower grades, identify the concrete limitation that prevents an A, even when no new work clears the value gate. Do not inflate or depress grades to avoid or manufacture recommendations. Call out materially different subsystem health instead of hiding it behind a broad grade. For ergonomics, distinguish package-consumer and contributor/operator experience when materially different; do not let a polished README conceal a broken supported first-use workflow.

Do not calculate an arithmetic overall score. Give a brief overall verdict instead; cosmetic strengths cannot offset a serious correctness or security problem. For repeat reviews, retain this rubric and compare grades only where scope and evidence are reasonably comparable. Explain changes rather than claiming false precision.

## 4. Apply the value gate before recommending work

A recommendation must answer all of these questions:

1. **What is the evidence?** Identify the concrete condition using verified file/line references and, where relevant, a test result, execution trace, caller analysis, or other direct evidence. Counts must be verified and labeled with their inspected scope.
2. **What is the project consequence?** Name a plausible failure mode, maintenance burden, inconsistency, blocked delivery, operating cost, or material risk. A prior incident is not required when a credible failure path is established. “Cleaner,” “more DRY,” and “best practice” are not sufficient benefits.
3. **What is the smallest useful intervention?** Prefer bounded removal or simplification over broad rewrites and new abstractions. Explain why the proposed scope is enough.
4. **Why act now?** Compare the expected benefit with implementation and ongoing maintenance cost, regression risk, opportunity cost, and leaving the code alone or changing it when next touched. State dependencies and compatibility concerns.
5. **Is it genuinely new and actionable?** Check existing work and decisions. Link an existing issue/PR instead of proposing a duplicate. If blocked, name the specific prerequisite and the next decision or validation that is worthwhile now.

Promote only findings supported strongly enough for their proposed next step. A bounded verification task may qualify when concrete evidence indicates material risk but does not yet establish the right fix; label it as verification, not a confirmed defect. Do not turn vague suspicions into implementation recommendations. Section 6 tests every candidate before it is reported.

### Recommendation rule for B-or-lower grades

- For each category graded **B, C, D, or F**, normally surface its **top 1–3 evidence-backed opportunities**, ranked by value. This is a per-category guideline, not a minimum quota. Return one strong opportunity rather than padding to three.
- When none clears the value gate, report **“No worthwhile new action identified”** and briefly explain why: the limitation is low impact, change risk exceeds benefit, relevant work is already tracked, or a named prerequisite must be resolved first. Preserve the grade; do not invent a ticket or force the category to A.
- For an already tracked or blocked improvement, reference the existing issue/PR or specific prerequisite and state the useful next decision. Do not recommend duplicate work or implementation before its prerequisites.
- For serious or critical weaknesses, make the risk and disposition explicit even when no immediate code change is justified. Consider bounded verification or containment only when it independently clears the value gate.
- Categories graded **A** normally need no improvement opportunities. **NE** calls for a specific evidence gap, not speculative cleanup; propose obtaining evidence only when that effort has material value. **N/A** requires no work.
- An opportunity spanning categories appears in full only under its primary category; other categories reference the same ID. Cross-references count toward their category's 1–3 opportunities.

### Ticket budget

Select up to three **overall priorities** from the category opportunities and the frontier bet, ranked by project value and material risk reduction; blocked and already tracked items are not eligible. Only these, plus verified critical alerts, are **Ready for ticketing**. Every other worthwhile opportunity is **Parked**: recorded with its evidence for a later decision, not ticketed now. A Plausible finding (section 6) may be selected only as a verification ticket. A supplied ticket budget replaces the default of three; never add items to fill it.

### Safeguards against low-value cleanup

- Consolidate code when it represents the same rule or behavior and should evolve together. Do not centralize coincidentally similar code that has different ownership or change drivers. Fewer lines are not sufficient justification.
- Before declaring code removable, investigate entrypoints, dynamic loading, reflection/code generation where relevant, CLI and scheduled tasks, build/deployment configuration, feature flags, and public/external consumers. A no-results text search or an unused-code tool warning is a lead, not proof.
- Before retiring a legacy implementation, establish required behavioral parity, consumer migration, deployment compatibility, and any rollback/support obligations. Intentional coexistence is not automatically a defect. Unknown retirement conditions are blockers, not permission to delete.
- Types do not by themselves prove that external data is safe. Do not remove checks at network, storage, user-input, or other trust boundaries merely because a static type looks restrictive.
- Do not recommend renaming folders, removing “V2” labels, adding documentation, increasing coverage, replacing libraries, or upgrading dependencies without a specific worthwhile outcome.
- Verify any time-sensitive support or vulnerability claim against authoritative current evidence when access is available. Otherwise label it unverified and do not invent status.
- Treat published APIs, schemas, persisted formats, and external integrations as compatibility-sensitive regardless of apparent implementation effort. Respect protected paths and label any proposal requiring approval.
- Grade the code that actually exists at the reviewed revision, even when a fix is already in flight. Existing work does not erase a weakness, but it may mean no additional ticket is needed.
- Do not manufacture recommendations to fill a quota. Do not create a speculative backlog of everything that might someday improve.
- Do not require a particular formatter, linter, type system, test framework, CI provider, aggregate command name, or agent-specific integration. Assess the safeguards and workflows the project actually needs. More tools, stricter rules, or extra pipeline steps are not automatically improvements.
- For ergonomics, show the concrete friction and its consequence: a supported example that fails, contradictory configuration, unnecessary repeated steps, hidden prerequisites, or a routine task requiring source-code archaeology. Do not equate fewer options or more documentation with better ergonomics without assessing the actual workflow.
- For checks, distinguish tool presence from effective protection. A script that ignores the relevant files, suppresses errors, or is never required is not equivalent to a working safeguard. Conversely, missing access to CI or branch-protection settings is an evidence limitation, not proof that enforcement is absent. Do not punish an appropriate tiered local/CI workflow merely because every environment does not run identical checks.
- Do not recommend an additional check without naming the issue class it would catch, the demonstrated gap in existing safeguards, and the expected maintenance/runtime cost. Formatting should support consistency, not become a proxy for correctness.
- A documentation/implementation disagreement does not establish which side is wrong. Use supported contracts, accepted requirements, relevant decisions, tests, and version history to distinguish **stale or incorrect documentation**, **implementation violating an intended guarantee**, and **unresolved contract conflict**. Tests corroborate behavior but do not alone establish the intended contract. Never recommend rewriting documentation merely to legitimize a bug or weakening a published guarantee without evaluating compatibility. Grade a confirmed implementation defect under correctness, not as a documentation defect.
- Recommend documentation work only when it prevents a concrete misunderstanding, integration failure, operational mistake, repeated investigation, or other material burden. Prefer correcting or consolidating the authoritative source over a new documentation site, more prose, per-function documentation, or automated documentation checks without a demonstrated payoff. Clearly labeled historical material does not need rewriting because behavior later changed.

### Change-friction safeguards

- **A smell is a reason to investigate, not a mandate to refactor.** For any reported weakness, identify the pattern, the concrete responsibility or supported change it complicates, the affected callers/state, and the maintenance burden or credible failure path. Prior incidents are not required, but imagined future requirements do not establish value.
- Use file length, function length, nesting, parameter counts, complexity metrics, and detector output as leads only. Do not deduct a grade or propose a ticket from a threshold alone. A long, cohesive function or explicit branching may be clearer and safer than additional helpers or polymorphism. Passing lint does not by itself establish a strong grade.
- Judge patterns in the project's language, framework, and operating context. Do not automatically reject switches, boolean options, primitives, exceptions, shared state, or framework-required structure; establish why a specific use creates a problem. Do not introduce patterns, wrappers, interfaces, classes, or schema layers merely to eliminate a label.
- Compare the smallest worthwhile change with leaving the code intact or improving it when next touched. Preserve supported behavior and identify appropriate regression evidence. Fewer lines, smaller files, fewer warnings, or a different abstraction are not sufficient acceptance outcomes on their own. If evidence instead reveals a behavior defect, separate the intended behavior correction from any optional refactoring.
- Support an A in readability, architecture, or duplication with representative evidence of cohesive responsibilities, explicit state/dependencies, and localized changes. An intentional pattern with no demonstrated downside need not lower a grade. Cross-reference existing findings rather than producing an exhaustive smell catalog or duplicate opportunities.

## 5. Assess capability and frontier position

Sections 2–4 ask whether the code is sound. This section asks how well the project does its job compared with the best known ways of doing that job today. Keep the two separate: frontier position never changes a category grade, and a strong grade never implies frontier capability.

1. **Core job and outcome.** State in one sentence what the project does and for whom, based on its documented purpose; label interpretation. Name the outcome that defines success—such as accuracy, latency, cost, reliability, autonomy, or revenue—and the evidence that it is achieved: evaluations, benchmarks, replays, production metrics, or recorded results. Descriptive claims in documentation are not capability evidence. Label measurements from earlier revisions as historical, with their producing revision, and establish whether they still apply to the reviewed code; if they do not, treat the capability as unmeasured. If the project's purpose does not call for frontier capability, say so in one line and skip the rest of this section.
2. **Reference set.** Identify 3–5 current reference points for the same job: leading open-source projects, commercial products, platform-native features, or published research, each cited with a source and date. Frontier claims are time-sensitive, and your own knowledge ends at your training cutoff. Verify them against current sources when web access is available; otherwise label them “unverified — model knowledge as of [cutoff]” and cap confidence at Low.
3. **Capability dimensions.** Choose the 3–6 dimensions that most determine success at this job. For each, compare the project's demonstrated capability with the strongest reference, using measured evidence on both sides where it exists. For model-driven projects, consider model currency, evaluation rigor, learning from outcomes, autonomy with guardrails, and cost per outcome; these are prompts for investigation, not a checklist.
4. **Frontier position.** Assign one position to the core capability, with High / Medium / Low confidence:

   | Position | Meaning |
   |---|---|
   | **Frontier-defining** | Measurably beyond the strongest reference on a dimension that matters for the project's purpose. |
   | **At frontier** | Matches the strongest references on the dimensions that matter. |
   | **Near frontier** | A current approach with a measured or well-evidenced gap on at least one important dimension. |
   | **Behind frontier** | Relies on a superseded approach where an accessible, materially better one exists. |
   | **NE — Not enough evidence** | The core capability is unmeasured, or the reference set cannot be verified. |

   Frontier-defining and At frontier require measured evidence for the project's side of the comparison. Compare with a prior review's position only when the reference set is comparable; a position can drop because the frontier moved rather than because the project regressed.
5. **Build versus adopt.** Flag components that rebuild a capability now available as a mature library, service, or platform feature without a differentiating advantage. Retiring one is a valid opportunity; weigh migration cost, lock-in, and data handling like any other change.

### Next frontier bet

Propose at most one frontier bet, **F1**, plus up to two alternatives considered, each with a one-line reason it was not chosen. F1 must clear the value gate with these substitutions: the evidence is the measured or cited gap to a named reference, the consequence is the outcome it moves, and the smallest useful change is the smallest decisive experiment rather than the full build. State:

- **Hypothesis:** adding X moves outcome M from a to at least b, or “baseline unknown — establish it first.”
- **Decisive experiment:** a spike, prototype, or evaluation run, with a time box.
- **Success and stop criteria:** the result that justifies building, and the result that ends the bet.
- **Prerequisites:** blocking findings by ID. A D or F in correctness, security, or test quality on the core workflow blocks the bet.

When the core capability cannot be measured, the bet is usually establishing that measurement; recommend it only when the capability is decision-relevant. If no bet clears the gate, say so. Do not turn the reference comparison into a feature backlog.

## 6. Verify before reporting

Before writing the output, try to refute every candidate opportunity, the frontier bet, every grade of C or lower, and any Frontier-defining or At frontier position. Use a fresh-context subagent for this pass when the agent supports one; otherwise make it a separate pass.

- Re-open each cited reference at the reviewed revision. Look for callers, guards, configuration, tests, or decisions that contradict the finding or shrink its consequence.
- For behavioral claims, try to reproduce the problem with a throwaway test, script, or command. Keep probes outside tracked files—in a scratch directory or the disposable copy—and never commit them. When execution is unsafe or unavailable, trace statically and say so.
- Label each opportunity **Confirmed** (reproduced, or traced end to end with direct evidence) or **Plausible** (credible but unproven; eligible only for a bounded verification ticket). Drop **Refuted** findings and adjust any grade or position that relied on them.

Report in the review context how many candidates were confirmed, downgraded, and refuted. A reproduction, when one exists, becomes the first acceptance check for its opportunity.

## 7. Required output

### Review context

In a compact paragraph, identify the repository/revision, scope, project assumptions, inspected critical workflows, execution environment, and material access or coverage limitations. Add one compact line stating which checks were run and their results, or that execution was unavailable, and one line with the verification-pass counts.

### Overall verdict

Use 2–4 sentences to summarize fitness for purpose, the most consequential supported concern if any, whether new work is justified, and the frontier position with its confidence (or that frontier capability is not a goal). Distinguish known strengths, known weaknesses, and important unknowns.

### Scorecard

| Category | Grade | Confidence | Evidence-backed assessment | Opportunities / disposition |
|---|---|---|---|---|

Include every core category and any justified optional categories. Keep each assessment to a concise strength/weakness statement with 1–3 verified source references. Use actual `path:line-range` references at the reviewed snapshot, plus relevant command results or issue/PR identifiers when needed. Do not invent line numbers. If precise references are unavailable, say so and use the most specific verifiable evidence.

For readability, architecture, and duplication, name the representative areas inspected and the evidence of localized, predictable changes or consequential friction, in plain language rather than smell terminology. For documentation, name the important claims or workflows sampled and the evidence of alignment or drift. Pair each material discrepancy with the exact documented claim—or, for an omission, the surfaces searched and the behavior users would otherwise have to discover—and the verified implementation evidence, and state the search scope; do not claim project-wide absence from a limited sample. Distinguish unverified claims from demonstrated contradictions; missing execution access is not itself documentation drift.

Use unique opportunity IDs **R1, R2, …** in the Opportunities / disposition column. For each B-or-lower category, reference its 1–3 worthwhile opportunities, an existing issue/PR, or **No worthwhile new action identified** with a concise reason. Do not omit category opportunities merely because they fall outside the overall priorities. Use **No action needed** for a supported A, **Insufficient evidence** for NE, and **Not applicable** for N/A; do not misrepresent an unassessed area as healthy.

### Verification evidence

Use a compact table for the applicable safeguards that materially support the checks grade:

| Safeguard | Command and effective scope | Result in this review | Execution / enforcement evidence |
|---|---|---|---|

Cover typechecking, linting, formatting, tests, evaluations, builds, and other relevant existing checks as applicable. Distinguish **Passed**, **Failed**, **Not run — reason**, **Confirmed absent**, and **Not applicable**; use **Unknown** when existence itself cannot be established. A defined command is not a passing result. Label historical CI results separately with their revision when known. Describe only verified execution/enforcement; report inaccessible merge requirements as unverified. Include concise source references or command outcomes, not raw logs.

### Frontier position

One line: the core capability, its position, and confidence—or that frontier capability is not a goal, with the reason. Then:

| Dimension | Project evidence | Strongest reference (source, date) | Gap |
|---|---|---|---|

Add any build-versus-adopt flags in one line below the table. Then give **F1** with its gap evidence, verification label, hypothesis, decisive experiment, success and stop criteria, prerequisites, status, and the alternatives considered—or state **No frontier bet clears the value gate** with the reason.

### Category opportunities — normally 1–3 for each B-or-lower category

Group worthwhile opportunities by their primary category, with higher-value categories first. Rank within each category by project value and material risk, not by how easy a change looks. Keep each opportunity compact and suitable for choosing a later drill-down; do not write full tickets or speculative implementation plans. Do not list Refuted findings. For each, use:

- **ID / title / primary category**
- **Verification:** Confirmed or Plausible, and whether it was reproduced, traced, or statically inspected.
- **Evidence and consequence:** What is wrong, where, and why it matters.
- **Smallest useful change:** The bounded intervention and important non-goals.
- **Value versus cost:** Expected benefit; effort S/M/L with a scope rationale; regression/compatibility risk; why this beats deferral or doing nothing. Do not invent savings.
- **Status:** Ready for ticketing (overall priorities and critical alerts only) / Parked / Already tracked in [reference] / Blocked by [specific prerequisite]. Identify approval needs and distinguish investigation from implementation.
- **Acceptance evidence:** 1–3 observable outcomes that would demonstrate the benefit and protect relevant existing behavior, starting with the reproduction when one exists. These should support a later ticket, not prescribe an uninvestigated full implementation.

When a B-or-lower category has no worthwhile new action, its scorecard disposition and explanation are sufficient; do not pad the opportunity section. When nothing clears the value gate anywhere, say: **“No new recommendations clear the value threshold for the reviewed scope.”** Do not fill the section with marginal ideas.

Do not hide additional verified critical exposures merely to satisfy a category's three-opportunity cap or the ticket budget. Flag them briefly as critical alerts, without expanding into a general backlog.

### Overall priorities — the ticket budget, 0–3

List up to three opportunity IDs, which may include F1, each with a one-sentence rationale. These and any critical alerts are the only items Ready for ticketing. Do not repeat the full findings or introduce new opportunities here. Omit the section when no opportunity clears the value gate.

### Deliberately leave alone — optional

Include only evidence-backed examples likely to tempt unnecessary cleanup, with the reason to retain them. Maximum three; omit the section when it adds no value. Do not turn it into a deferred task list.

### Next decision

End with one concrete next decision: the first ticket to create, continuing an existing effort, running the frontier experiment, resolving a meaningful evidence gap, or leaving the project unchanged. Do not force “quick win,” “medium-effort win,” or “cleanup to avoid” summaries when none are justified.

## Output discipline

Be direct, concise, and evidence-led. State uncertainty precisely rather than hiding it or hedging vaguely. Do not restate this prompt, narrate every file opened, or produce an exhaustive smell inventory or feature wishlist. No changes or ticket creation are authorized by this review.
