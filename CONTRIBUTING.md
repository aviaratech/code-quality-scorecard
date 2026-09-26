# Contributing

Thank you for helping improve the Code Quality Scorecard.

The project optimizes for an assessment that is evidence-led, portable across repositories and agent environments, and resistant to low-value cleanup work. Changes to the rubric should improve decision quality without turning it into a style guide, tool checklist, or backlog generator.

## Principles to preserve

Contributions should preserve these properties:

1. **Evidence before judgment.** Grades and recommendations must be supported by repository-specific evidence rather than generic best-practice claims.
2. **Fitness for purpose.** Assess the project's actual responsibilities, maturity, users, and risk rather than an idealized greenfield architecture.
3. **Grades and recommendations are separate.** A non-A grade may accurately describe a weakness while no immediate work is worth doing.
4. **Value gate before work.** Recommendations require a concrete consequence and a bounded intervention whose expected value beats deferral or doing nothing.
5. **Tool neutrality.** Do not require a particular model, GitHub integration, CLI, CI provider, linter, formatter, package manager, test framework, or agent plugin unless the requirement is itself part of an explicitly optional adapter.
6. **Read-only assessment.** The canonical scorecard does not edit code, install or upgrade dependencies, deploy, or create tickets/PRs.
7. **No quota filling.** The rubric must allow a strong project to produce no recommendations.
8. **Uncertainty is explicit.** Missing access lowers confidence or produces `NE`; it is not evidence of a defect.

## Proposing a rubric change

A rubric change should explain:

- the failure mode in the current rubric;
- an example of the incorrect or low-value outcome it can produce;
- the smallest wording or structural change that addresses that failure mode;
- likely false positives or unintended incentives introduced by the change; and
- whether the change affects grading semantics, required output, or recommendation behavior.

Prefer targeted changes over adding more categories, thresholds, metrics, or process requirements.

## Versioning

The current rubric version is declared in `PROMPT.md`.

Changes that materially alter category meaning, grade semantics, recommendation rules, or required output should bump the rubric version and add an entry to `CHANGELOG.md`. Editorial clarifications that do not change assessment behavior may be recorded without a rubric-version bump.

## Examples

When a change affects expected output, update or add a synthetic example that demonstrates the behavior without depending on a private repository.

Examples should illustrate the rubric; they should not become hidden grading rules.
