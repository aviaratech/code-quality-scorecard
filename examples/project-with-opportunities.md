# Example — Project With Opportunities

> Synthetic example. This is intentionally abbreviated while preserving the structure expected from the rubric.

## Review context

Reviewed `example/event-worker` at commit `4d918ce` on 2026-10-07. Scope covered worker startup/configuration, event ingestion, retry/dead-letter behavior, contributor setup, CI validation, operator documentation, and representative tests. The review ran in a sandboxed disposable clone at `4d918ce`: dependencies were installed from the lockfile with lifecycle scripts disabled, no credentials were reachable, and network access was limited to the package registry. The reviewed checkout had no tracked-file changes afterward. Production infrastructure and repository merge settings were not accessed.

Checks run: typecheck passed; lint passed; tests passed; build passed. No formatting check was defined.

Verification pass: three candidates tested. Two were confirmed (R1 reproduced, R2 traced). One was refuted: a suspected unhandled rejection in `src/transport/index.ts:30-44` is caught by the worker's error boundary (`src/worker/process.ts:88-97`).

## Overall verdict

The worker's core processing path is sound and well tested, but supported first-use and validation workflows have avoidable friction, and the operator documentation omits a consequential retry/dead-letter behavior that must currently be discovered from code. Focused work is justified in ergonomics and documentation; the absence of a standalone formatting check does not independently clear the value gate. Frontier capability is not a goal for this internal worker.

## Scorecard

| Category | Grade | Confidence | Evidence-backed assessment | Opportunities / disposition |
|---|---|---|---|---|
| Correctness and contract safety | A | High | Event parsing, idempotency, retry transitions, and dead-letter routing are explicit and protected by tests (`src/worker/process.ts:38-126`, `src/retry/policy.ts:17-79`, `test/process.integration.test.ts:61-174`). | No action needed |
| Readability and simplicity | A | Medium | Representative processing and retry paths are straightforward, and retry changes stay within the policy module and its tests (`src/worker/process.ts:38-126`, `src/retry/policy.ts:17-79`, `test/process.integration.test.ts:61-174`). | No action needed |
| Architecture and discoverability | A | High | Transport, domain handling, retry policy, and persistence are separated with clear dependency direction (`src/transport/index.ts:1-46`, `src/worker/process.ts:1-37`, `src/storage/repository.ts:1-68`). | No action needed |
| Duplication and abstraction quality | A | Medium | Shared retry rules and event contracts have single owners; similar handlers retain distinct domain behavior (`src/retry/policy.ts:17-79`, `src/contracts/event.ts:12-58`). | No action needed |
| Dead code and migration hygiene | A | Medium | Every script entrypoint resolves to maintained source, and ingestion and replay share the single retry policy; no superseded runtime path remains (`package.json:31-52`, `src/retry/policy.ts:17-79`, `src/worker/process.ts:1-37`). | No action needed |
| Ergonomics and ease of use | C | High | The documented local start path fails without manually creating an undocumented environment file and queue fixture; contributors must inspect source and test helpers to discover both prerequisites (`README.md:24-51`, `src/config/load.ts:19-47`, `test/helpers/queueFixture.ts:8-42`). | R1 |
| Documentation accuracy and behavioral alignment | B | High | Operator docs describe retries but omit the supported dead-letter threshold and replay side effect that are explicit in configuration and tests (`docs/operations.md:63-88`, `src/retry/policy.ts:17-79`, `test/replay.integration.test.ts:44-121`). | R2 |
| Automated checks and quality gates | B | High | Typecheck, lint, tests, and build execute successfully. No formatting check exists, but no demonstrated defect class or material workflow cost currently justifies adding one (`package.json:31-52`, `.github/workflows/ci.yml:16-58`). | No worthwhile new action identified — absence alone does not clear the value gate |
| Test quality and behavior protection | A | High | Tests protect ingestion, idempotency, retry exhaustion, replay, and representative storage failures (`test/process.integration.test.ts:61-174`, `test/replay.integration.test.ts:44-121`). | No action needed |
| Dependency health and build reproducibility | A | High | Runtime/toolchain versions and lockfile are consistent; clean build succeeds in the reviewed environment (`package.json:8-30`, `package-lock.json`, `tsconfig.build.json:1-27`). | No action needed |
| Security and privacy fundamentals | A | Medium | External payload validation and log redaction are explicit in inspected paths; production authorization was outside scope (`src/contracts/event.ts:12-58`, `src/logging/redact.ts:11-49`). | No action needed |

## Verification evidence

| Safeguard | Command and effective scope | Result in this review | Execution / enforcement evidence |
|---|---|---|---|
| Typechecking | `npm run typecheck` — maintained TypeScript source | Passed | Executed locally; CI definition inspected |
| Linting | `npm run lint` — source and tests | Passed | Executed locally; CI definition inspected |
| Formatting | No standalone check found | Confirmed absent | Package scripts and CI workflow inspected |
| Tests | `npm test` — unit and integration tests | Passed | Executed locally; CI definition inspected |
| Build | `npm run build` — worker artifact | Passed | Executed locally; CI definition inspected |
| Merge enforcement | Repository settings | Unknown | Settings unavailable |

## Frontier position

Frontier capability is not a goal: the worker is internal infrastructure whose documented purpose is reliable event processing for one product (`README.md:1-22`).

## Category opportunities

### R1 — Make the supported local startup path self-contained

- **Primary category:** Ergonomics and ease of use
- **Verification:** Confirmed — reproduced. Following `README.md:24-51` in the disposable clone fails at startup with a missing-configuration error raised by `src/config/load.ts:19-47`.
- **Evidence and consequence:** The README's startup command requires an environment file and queue fixture that are not created or documented by the supported setup path (`README.md:24-51`, `src/config/load.ts:19-47`, `test/helpers/queueFixture.ts:8-42`). A new contributor following the documented workflow reaches startup failures and must inspect source/test helpers to recover.
- **Smallest useful change:** Make the existing local bootstrap command create or validate the required development-only prerequisites, or fail with one actionable message that names the exact supported setup command. Do not introduce a new local orchestration platform.
- **Value versus cost:** Medium benefit, small effort. This removes repeated first-use investigation without changing runtime architecture; regression risk is low if production configuration remains untouched.
- **Status:** Ready for ticketing (overall priority 1).
- **Acceptance evidence:** The reproduction now succeeds: a clean disposable clone follows the documented setup/start sequence without source-code archaeology; missing prerequisites fail with actionable guidance; existing worker tests remain green.

### R2 — Document retry exhaustion and replay behavior in the operator guide

- **Primary category:** Documentation accuracy and behavioral alignment
- **Verification:** Confirmed — traced. The threshold and replay side effect are defined in `src/retry/policy.ts:17-79` and asserted in `test/replay.integration.test.ts:44-121`; `docs/operations.md:63-88` omits both.
- **Evidence and consequence:** Runtime configuration and tests establish the retry exhaustion threshold, dead-letter transition, and replay side effect, while the operator guide describes only generic retries (`docs/operations.md:63-88`, `src/retry/policy.ts:17-79`, `test/replay.integration.test.ts:44-121`). Operators can otherwise make an incorrect recovery assumption during an incident.
- **Smallest useful change:** Update the existing authoritative operator section with the supported threshold source, dead-letter disposition, replay effect, and relevant command/reference. Do not add a second runbook.
- **Value versus cost:** Medium benefit, small effort. The change reduces operational ambiguity with little maintenance cost because it consolidates behavior into the existing authoritative surface.
- **Status:** Ready for ticketing (overall priority 2).
- **Acceptance evidence:** The operator guide states the behavior that matches configuration/tests; a reviewer can trace each consequential claim to the current implementation; no duplicate operational guide is introduced.

## Overall priorities — the ticket budget

1. **R1** — Removes a verified failure in the supported contributor first-use path with bounded implementation risk.
2. **R2** — Prevents a concrete incident-response misunderstanding by aligning the authoritative operator guide with runtime behavior.

## Next decision

Create tickets for R1 and R2 using the target repository's own issue conventions; do not create work solely for the missing formatting check.
