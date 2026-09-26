# Value-Driven Code Quality Scorecard

Rubric version: 4.0

## Role and objective

Act as a Principal/Staff engineer reviewing this repository. Produce an evidence-backed code-quality scorecard. For **each category graded B or lower**, normally provide its **top 1–3 worthwhile improvement opportunities**, subject to the value gate below. Also identify the **0–3 highest-priority opportunities across the entire review** as a navigation summary, not a cap on the category-level findings.

The purpose is to identify worthwhile work, not to generate cleanup tasks, maximize abstraction, or make every category an A. A review with no recommended changes is a successful outcome when the evidence supports it.

**Grades describe the current technical condition. Recommendations separately account for benefit, effort, risk, timing, and project priorities. A non-A grade does not automatically justify a ticket.**

This is an assessment, not an implementation task. Do not edit tracked files, apply fixes, create tickets or PRs, install or upgrade dependencies, deploy, or change external systems. Do not print secrets or sensitive data.

## Optional overrides

Use the defaults below without requiring clarification. Apply any supplied project purpose, critical workflows, review scope, priorities, protected paths, compatibility commitments, or prior scorecard. Obtain these from repository documentation when available; do not invent them.

## 1. Establish context and coverage

- Read applicable repository guidance, including `AGENTS.md`, `CLAUDE.md`, `CONSTITUTION.md`, README files, and relevant architecture decisions when present. Respect project-specific priorities and protected items. Missing optional documents are not defects.
- Record the review date, branch, commit SHA, and whether local modifications affect the reviewed snapshot. If an identifier cannot be verified, mark it unavailable rather than guessing.
- Identify the project's purpose, maturity, supported environments, critical workflows, package boundaries, public interfaces, and established validation commands. Identify applicable audiences: package consumers, contributors, operators, and agents. Label assumptions and distinguish documented requirements from your interpretation.
- Review available adjacent issues, open and recently merged PRs, relevant git history, and recorded decisions. Check whether apparent debt is intentional, already tracked, being migrated, or previously rejected. Use available tooling rather than assuming a particular memory service or GitHub CLI exists. Report access gaps once.
- Default to a **repository-wide inventory with representative, risk-based inspection**. Trace critical workflows through their entrypoints, boundaries, implementation, and tests. Sample shared code and representative secondary areas; do not base a repo-wide grade on a few convenient files.
- If scope was explicitly supplied, stay within it. For large repositories, identify the reviewed subsystems and gaps. Do not require a scope-selection conversation before producing a useful scorecard. Never imply that sampling is exhaustive.
- Inspect scripts before running them. Run existing, non-fixing local validation commands when safe and supported. Disposable local build/test artifacts are acceptable; production access, external writes, destructive tests, migrations, and dependency changes are not. If a check is unsafe or unavailable, report that limitation instead of running it.
- Distinguish checks executed during this review from historical results or static inspection. Report actual outcomes, failures, and environmental blockers. A passing check is evidence for what it tests, not proof of whole-project correctness.
- Trace an applicable first-use workflow: package installation/import and a minimal consumer example, or application/runtime setup, configuration, startup, validation, and troubleshooting. Use existing safe environments and test fixtures; do not install dependencies or access production to complete this review. Distinguish documented instructions, inspected configuration, and directly verified behavior. Do not assume an internal workspace import proves the distributed package is usable.
- Inventory applicable checks and trace their effective scope and execution: types, lint, formatting, tests, builds, and other existing risk-relevant safeguards. Inspect exclusions, command chaining, failure propagation, workflow triggers, and bypasses. Distinguish a defined script, an executed CI job, and a verified merge requirement; do not claim enforcement from configuration alone.
- Identify applicable documentation surfaces: README and onboarding guides, API/CLI references, examples, configuration templates, consequential comments/docstrings, architecture descriptions, operator runbooks, and agent instructions. Include published documentation when relevant and accessible; state access gaps. Match claims to their intended revision, release, and environment. Do not confuse clearly labeled historical decisions or planned features with promises about current behavior.
- Cross-check representative, consequential documentation claims against implementation, configuration, relevant tests, and safe execution where available. Check both directions: do documented features and guarantees hold, and are important supported behaviors, prerequisites, limitations, defaults, failure modes, and side effects explained sufficiently for the intended audience? Record what was inspected versus executed; do not claim an example works merely because it looks plausible.

- Investigate code-smell candidates in representative maintained code by tracing relevant callers, state transitions, and likely changes supported by current responsibilities, requirements, or history. Inspect context before accepting a detector warning or smell label. Distinguish maintained source from generated/vendor output; do not recommend hand-editing generated code to satisfy a heuristic.

## 2. Evaluate these categories

| Category | Evaluate |
|---|---|
| **Correctness and contract safety** | Important invariants, state transitions, data integrity, async behavior, error handling, cleanup, and input/output contracts. Assess runtime validation at trust boundaries and consistency between types, schemas, and behavior. |
| **Readability and simplicity** | Whether a maintainer can follow the code's intent and control flow; naming, local complexity, side effects, indirection, responsibility size, and explanations of non-obvious decisions. Reward clarity, not shortness or a particular coding style. |
| **Code smells and change friction** | Structural and behavioral warning signs that make supported changes harder or riskier: oversized or mixed-responsibility units; deeply nested or flag-driven logic; long positional parameter lists; hidden mutable state, side effects, or call-order dependencies; inappropriate access to another module's internals; one logical change scattered across unrelated locations; unclear domain values; and speculative indirection. Treat these as investigation signals, not automatic defects. Grade demonstrated maintenance burden or credible failure risk, not smell counts or arbitrary size thresholds. |
| **Architecture and discoverability** | Cohesive responsibilities, dependency direction, coupling, cycles, package boundaries, public interfaces, and filesystem navigation. Compare with the project's stack and established conventions; require a concrete consequence before treating a different folder layout as a defect. |
| **Duplication and abstraction quality** | Repeated business rules, contracts, state machines, and behavior that should stay consistent; existing abstractions that create unnecessary indirection or coupling. Distinguish shared semantics from superficially similar code. Assess both under-abstraction and over-abstraction. |
| **Dead code and migration hygiene** | Unreachable or obsolete code, unused dependencies/configuration/assets, retired flags and shims, competing implementations, partially completed migrations, and legacy paths that impose real cost. Assess whether retirement preconditions are met. Versioned names alone are not debt. |
| **Ergonomics and ease of use** | For packages: installation/import, discoverable APIs, sensible defaults, configuration clarity, useful errors, accurate examples, and applicable type information. For apps/runtimes: discoverable setup, configuration, startup/shutdown, local development, validation, and troubleshooting workflows for humans and agents. Assess prerequisites, hidden state, manual steps, non-interactive execution where appropriate, and reliance on undocumented knowledge. Judge applicable consumer and contributor/operator journeys; do not require every project to support every audience. |
| **Documentation accuracy and behavioral alignment** | Whether documentation faithfully describes supported functionality and actual behavior for its intended version and audience. Assess setup instructions, API/CLI examples, configuration/defaults, guarantees, limitations, error behavior, side effects, architecture, and operational/agent guidance as applicable. Identify misleading claims, contradictory sources, obsolete instructions, and consequential omissions. Reward accurate, sufficient guidance—not document volume or exhaustive coverage of internal symbols. |
| **Automated checks and quality gates** | Whether applicable typechecking, linting, formatting checks, tests, builds, and other justified safeguards actually cover the relevant code, execute reliably, and report failures. Assess discoverable local commands, appropriate CI execution, failure propagation, intentional versus accidental exclusions, bypasses, and signal-to-noise. Judge whether required validation is enforced at the appropriate stage; do not require every check on every edit. |
| **Test quality and behavior protection** | Whether tests meaningfully protect critical behavior, contracts, failure paths, and likely changes; appropriate integration/boundary coverage, representative assertions, and manageable test maintenance. Judge what the tests would detect, not merely whether they execute. Do not use coverage percentage or test counts as automatic grades. |
| **Dependency health and build reproducibility** | Dependency justification, unnecessary or conflicting dependencies, relevant support/security status when verifiable, lockfiles and supported toolchain consistency where applicable, repeatable builds, and distribution/artifact correctness. An older version is not a defect solely because a newer version exists. |
| **Security and privacy fundamentals** | Applicable trust boundaries, authorization, sensitive-data handling, exposed secrets, injection risks, unsafe defaults, and logging/telemetry privacy. This is a scoped engineering review, not a security certification. |

Add **performance/resource efficiency** or **operational diagnosability** only when relevant to documented requirements, critical workflows, or concrete evidence. Explain why an optional category was included. Do not expand this into an unrelated product audit.

**Keep the category boundaries explicit:** ergonomics concerns how easily someone can use and work with the project; documentation concerns whether its explanations and instructions are accurate and sufficient for the intended audience; automated checks concern whether safeguards run, cover the intended scope, and surface or block failures; test quality concerns which important behaviors those tests actually protect. Dependency/build health concerns the underlying dependency and build mechanics. An awkward interface can be documented accurately, and an intuitive interface can have misleading documentation. Cross-reference shared findings rather than recommending the same fix twice.

**Code smells are a cross-cutting diagnostic category, not a second defect inventory.** Readability assesses how readily existing code can be understood; code smells assess evidence of fragile responsibilities, state, and change patterns. Architecture and duplication findings remain in their most specific primary category when appropriate. Cross-reference their IDs from the code-smells row; do not repeat the finding or lower another grade solely because a smell name also applies. A separate grade impact requires an explicitly distinct, supported consequence.

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

Promote only findings supported strongly enough for their proposed next step. A bounded verification task may qualify when concrete evidence indicates material risk but does not yet establish the right fix; label it as verification, not a confirmed defect. Do not turn vague suspicions into implementation recommendations.

### Recommendation rule for B-or-lower grades

- For each category graded **B, C, D, or F**, normally surface its **top 1–3 evidence-backed opportunities**, ranked by value. This is a per-category guideline, not a minimum quota. Return one strong opportunity rather than padding to three.
- When none clears the value gate, report **“No worthwhile new action identified”** and briefly explain why: the limitation is low impact, change risk exceeds benefit, relevant work is already tracked, or a named prerequisite must be resolved first. Preserve the grade; do not invent a ticket or force the category to A.
- For an already tracked or blocked improvement, reference the existing issue/PR or specific prerequisite and state the useful next decision. Do not recommend duplicate work or implementation before its prerequisites.
- For serious or critical weaknesses, make the risk and disposition explicit even when no immediate code change is justified. Consider bounded verification or containment only when it independently clears the value gate.
- Categories graded **A** normally need no improvement opportunities. **NE** calls for a specific evidence gap, not speculative cleanup; propose obtaining evidence only when that effort has material value. **N/A** requires no work.
- An opportunity spanning categories appears in full only under its primary category; other categories reference the same ID. Cross-references count toward their category's 1–3 opportunities.
- Separately select **up to three overall priorities** from the category opportunities. This summary guides attention; it must not suppress worthwhile opportunities in other B-or-lower categories.

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
- A documentation/implementation disagreement does not establish which side is wrong. Use supported contracts, accepted requirements, relevant decisions, tests, and version history to distinguish **stale or incorrect documentation**, **implementation violating an intended guarantee**, and **unresolved contract conflict**. Tests corroborate behavior but do not alone establish the intended contract. Never recommend rewriting documentation merely to legitimize a bug or weakening a published guarantee without evaluating compatibility. Grade a confirmed implementation defect primarily under correctness, not as a documentation defect solely because the documented contract is unmet.
- For documentation findings, pair the exact documented claim with verified implementation/configuration/test or execution evidence and explain the consequence for a consumer, contributor, operator, or agent. For an omission, identify the relevant documentation surface searched and the important behavior users would otherwise have to discover. State the search scope; do not claim project-wide absence from a limited sample.
- Recommend documentation work only when it prevents a concrete misunderstanding, integration failure, operational mistake, repeated investigation, or other material burden. Prefer correcting or consolidating an authoritative source and linking to it over duplicating explanations. Do not require a new documentation site, more prose, documentation for every function, or automated documentation checks without a demonstrated payoff. Clearly labeled historical material does not need rewriting simply because behavior later changed.

### Code-smell assessment safeguards

- **A smell is a reason to investigate, not a mandate to refactor.** For any reported weakness, identify the pattern, the concrete responsibility or supported change it complicates, the affected callers/state, and the maintenance burden or credible failure path. Prior incidents are not required, but imagined future requirements do not establish value.
- Use file length, function length, nesting, parameter counts, complexity metrics, and detector output as leads only. Do not deduct a grade or propose a ticket from a threshold alone. A long, cohesive function or explicit branching may be clearer and safer than additional helpers or polymorphism. Passing lint does not by itself establish a strong code-smells grade.
- Judge patterns in the project's language, framework, and operating context. Do not automatically reject switches, boolean options, primitives, exceptions, shared state, or framework-required structure; establish why a specific use creates a problem. Do not introduce patterns, wrappers, interfaces, classes, or schema layers merely to eliminate a label.
- Compare the smallest worthwhile change with leaving the code intact or improving it when next touched. Preserve supported behavior and identify appropriate regression evidence. Fewer lines, smaller files, fewer warnings, or a different abstraction are not sufficient acceptance outcomes on their own. If evidence instead reveals a behavior defect, separate the intended behavior correction from any optional refactoring.
- Grade the severity and reach of substantiated change friction, not the number of smell labels. Support an A with representative evidence of cohesive responsibilities, explicit state/dependencies, and localized changes. An intentional pattern with no demonstrated downside need not lower the grade. Cross-reference existing findings rather than producing an exhaustive smell catalog or duplicate opportunities.

## 5. Required output

### Review context

In a compact paragraph, identify the repository/revision, scope, project assumptions, inspected critical workflows, and material access or coverage limitations. Add one compact line stating which checks were run and their results, or that execution was unavailable.

### Overall verdict

Use 2–3 sentences to summarize fitness for purpose, the most consequential supported concern if any, and whether new work is justified. Distinguish known strengths, known weaknesses, and important unknowns.

### Scorecard

| Category | Grade | Confidence | Evidence-backed assessment | Opportunities / disposition |
|---|---|---|---|---|

Include every core category and any justified optional categories. Keep each assessment to a concise strength/weakness statement with 1–3 verified source references. Use actual `path:line-range` references at the reviewed snapshot, plus relevant command results or issue/PR identifiers when needed. Do not invent line numbers. If precise references are unavailable, say so and use the most specific verifiable evidence.

For the code-smells grade, briefly identify the representative areas inspected and the evidence of localized, predictable changes or consequential friction. Describe the impact in plain language rather than relying on smell terminology. Reference shared opportunity IDs; detector totals alone are not a supported assessment.

For the documentation grade, briefly identify the important claims or workflows sampled and the evidence of alignment or drift. Include paired documentation and implementation references for material discrepancies in the assessment or its linked opportunity. Distinguish unverified claims from demonstrated contradictions; missing execution access is not itself documentation drift.

Use unique opportunity IDs **R1, R2, …** in the Opportunities / disposition column. For each B-or-lower category, reference its 1–3 worthwhile opportunities, an existing issue/PR, or **No worthwhile new action identified** with a concise reason. Do not omit category opportunities merely because they fall outside the overall top three. Use **No action needed** for a supported A, **Insufficient evidence** for NE, and **Not applicable** for N/A; do not misrepresent an unassessed area as healthy.

### Verification evidence

Use a compact table for the applicable safeguards that materially support the checks grade:

| Safeguard | Command and effective scope | Result in this review | Execution / enforcement evidence |
|---|---|---|---|

Cover typechecking, linting, formatting, tests, builds, and other relevant existing checks as applicable. Distinguish **Passed**, **Failed**, **Not run — reason**, **Confirmed absent**, and **Not applicable**; use **Unknown** when existence itself cannot be established. A defined command is not a passing result. Label historical CI results separately with their revision when known. Describe only verified execution/enforcement; report inaccessible merge requirements as unverified. Include concise source references or command outcomes, not raw logs.

### Category opportunities — normally 1–3 for each B-or-lower category

Group worthwhile opportunities by their primary category, with higher-value categories first. Rank within each category by project value and material risk, not by how easy a change looks. Keep each opportunity compact and suitable for choosing a later drill-down; do not write full tickets or speculative implementation plans. For each, use:

- **ID / title / primary category**
- **Evidence and consequence:** What is wrong, where, and why it matters.
- **Smallest useful change:** The bounded intervention and important non-goals.
- **Value versus cost:** Expected benefit; effort S/M/L with a scope rationale; regression/compatibility risk; why this beats deferral or doing nothing. Do not invent savings.
- **Status:** Ready for ticketing / Already tracked in [reference] / Blocked by [specific prerequisite]. Identify approval needs and distinguish investigation from implementation.
- **Acceptance evidence:** 1–3 observable outcomes that would demonstrate the benefit and protect relevant existing behavior. These should support a later ticket, not prescribe an uninvestigated full implementation.

When a B-or-lower category has no worthwhile new action, its scorecard disposition and explanation are sufficient; do not pad the opportunity section. When nothing clears the value gate anywhere, say: **“No new recommendations clear the value threshold for the reviewed scope.”** Do not fill the section with marginal ideas.

Do not hide additional verified critical exposures merely to satisfy a category's three-opportunity cap. Flag them briefly as critical alerts, without expanding into a general backlog.

### Overall priorities — 0–3 across categories

Identify the up to three opportunities with the highest project value and material risk reduction. Reference their IDs with a one-sentence rationale each; do not repeat the full findings or introduce new opportunities here. This is the recommended order for follow-up, not a substitute for the category-level opportunities. Omit the section when no opportunity clears the value gate.

### Deliberately leave alone — optional

Include only evidence-backed examples likely to tempt unnecessary cleanup, with the reason to retain them. Maximum three; omit the section when it adds no value. Do not turn it into a deferred task list.

### Next decision

End with one concrete next decision: the best category/recommendation to investigate, continuing an existing effort, resolving a meaningful evidence gap, or leaving the project unchanged. Do not force “quick win,” “medium-effort win,” or “cleanup to avoid” summaries when none are justified.

## Output discipline

Be direct, concise, and evidence-led. State uncertainty precisely rather than hiding it or hedging vaguely. Do not restate this prompt, narrate every file opened, or produce an exhaustive smell inventory. No changes or ticket creation are authorized by this review.
