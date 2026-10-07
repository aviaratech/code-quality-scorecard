# Example — Model-Driven Service

> Synthetic example. This is intentionally abbreviated while preserving the structure expected from the rubric. The frontier reference points are placeholders, not real products or papers.

## Review context

Reviewed `example/docs-answer-service` at commit `91c0e4b` on 2026-10-07. The service answers employees' questions from indexed internal documents, with citations. Scope covered ingestion, retrieval, answer orchestration, tool calls, the answer-quality evaluation set, CI, the README, and the operator guide. The review ran in a sandboxed fresh clone at `91c0e4b`. Writes were confined to the clone and sandbox temporary directories, no credentials were reachable, and the only network access was to the package registry while fetching locked dependencies with lifecycle scripts disabled, so no live model calls were made. The original checkout had no tracked-file changes afterward. Production telemetry and repository settings were not accessible.

Checks run: typecheck, lint, formatting, unit tests (212), and build passed. Answer-quality evaluations and the optional SharePoint ingest package were not run because they require credentials. The only evaluation results are historical: the committed 2026-09-18 run, produced at `5be21d0`.

Verification pass: seven candidate opportunities tested. Four were confirmed (R1 and R2 reproduced with throwaway tests against the stubbed model client; R3 and R4 traced), one was downgraded to Plausible (R5), and two were refuted: a suspected cache race is guarded by a per-key lock (`src/cache/store.ts:40-58`), and a suspected unused dependency is loaded dynamically (`src/ingest/loaders.ts:12-30`). F1 is Plausible: the references justify testing hybrid retrieval, but no comparable measurement shows that it would help this corpus.

## Overall verdict

The service works and is reasonably well structured, but two confirmed defects matter: retrieved document text can drive a tool that sends email without confirmation, and merged chunks produce wrong citations. Three focused tickets are justified. The frontier position is **NE**: the project's own retrieval measurement is historical but still applies, while the references report results only on unrelated public benchmarks. Whether their approaches would help this corpus is the hypothesis the frontier bet tests, and the bet waits on the security fix.

## Scorecard

| Category | Grade | Confidence | Evidence-backed assessment | Opportunities / disposition |
|---|---|---|---|---|
| Correctness and contract safety | C | High | Chunk merging keeps only the first chunk's offsets, so citations can point at the wrong passage (reproduced; see R2). The historical 2026-09-18 evaluation run flagged 23 of 180 answers for citation mismatches; the current rate is unmeasured, but the reproduction shows the defect persists (`src/retrieval/merge.ts:31-57`, `evals/results/2026-09-18.json`). | R2 |
| Readability and simplicity | B | Medium | Retrieval and ingestion modules are easy to follow. Answer orchestration mixes retrieval, prompt assembly, and tool dispatch in one function, so a change to tool handling requires reading retrieval code (`src/answer/orchestrate.ts:30-212`). | No worthwhile new action identified — the friction is local, and R1 already changes this path |
| Architecture and discoverability | A | Medium | Ingestion, retrieval, and answering are separate packages with one-way dependencies and a single service entrypoint (`src/index.ts:1-40`, `src/retrieval/index.ts:1-22`, `src/answer/index.ts:1-18`). | No action needed |
| Duplication and abstraction quality | A | Medium | Prompt templates and citation formatting each have one owner used by both the API and the chat handler (`src/answer/prompt.ts:1-58`, `src/answer/cite.ts:1-44`). | No action needed |
| Dead code and migration hygiene | B | Medium | The recorded migration to the current embedding pipeline left the legacy job scheduled with no recorded owner, consumer, or retirement condition, so it re-embeds the full corpus every night. Whether anything outside the repository reads its index is unknown; that gap is R5's verification question, not part of the grade (`docs/adr/0007-embedding-pipeline.md:1-36`, `infra/schedule.yaml:12-19`, `scripts/embed-legacy.ts:1-88`). | R5 |
| Ergonomics and ease of use | B | High | Local setup with the mock model client works as documented. Running evaluations needs a model API key that only the operator guide mentions (`README.md:10-48`, `docs/operations.md:30-41`). | No worthwhile new action identified — low impact; the evaluation runner's error names the missing key |
| Documentation accuracy and behavioral alignment | C | High | The README promises that every answer cites its sources, but the orchestrator returns an uncited answer when retrieval finds nothing (`README.md:5-9`, `src/answer/orchestrate.ts:150-171`). | R4 |
| Automated checks and quality gates | B | High | Types, lint, formatting, unit tests, and build run in CI; nothing runs the answer-quality evaluations (`.github/workflows/ci.yml:1-64`). | R3 (cross-reference) |
| Test quality and behavior protection | C | High | Unit tests cover retrieval and formatting, but answer quality is measured only by evaluations run by hand; nine prompt and answer-formatting changes have merged since the last committed result (`evals/README.md:1-40`, `git log 5be21d0..91c0e4b -- src/answer/`). | R3 |
| Dependency health and build reproducibility | A | Medium | The lockfile, toolchain declaration, frozen install, and clean build are consistent for every workspace except the optional SharePoint ingest package, whose private registry needs credentials; that package was not verified, which lowers confidence rather than the grade (`package.json:8-31`, `package-lock.json`, `packages/ingest-sharepoint/package.json:12-24`). | No action needed |
| Security and privacy fundamentals | D | High | Retrieved document text is placed in the system prompt without delimiting, and the always-registered email tool sends without user confirmation. The index includes HR and finance folders (`src/answer/prompt.ts:22-58`, `src/tools/email.ts:10-44`, `config/sources.yaml:3-21`). | R1 |
| Performance/resource efficiency (optional) | NE | Low | Included because the README sets a 3-second p95 answer target (`README.md:52-55`); no latency measurement or production metric was accessible. | Insufficient evidence |

## Verification evidence

| Safeguard | Command and effective scope | Result in this review | Execution / enforcement evidence |
|---|---|---|---|
| Typechecking | `npm run typecheck` — all workspaces except the optional SharePoint package | Passed | Executed in the sandboxed clone; CI job defined |
| Linting | `npm run lint` — source and tests | Passed | Executed; CI job defined |
| Formatting | `npm run format:check` — tracked source and docs | Passed | Executed; CI job defined |
| Unit tests | `npm test` — 212 tests | Passed | Executed; CI job defined |
| Answer-quality evaluations | `npm run eval` — 180 questions | Not run — requires a model API key | No CI job; historical result from 2026-09-18 at `5be21d0` (`evals/results/2026-09-18.json`) |
| Build | `npm run build` — service bundle | Passed | Executed; CI job defined |
| SharePoint package build | `npm run build -w ingest-sharepoint` | Not run — requires private registry credentials | CI job defined |
| Merge enforcement | Repository settings | Unknown | Settings unavailable |

## Frontier position

Core capability: answering employees' questions from internal documents with correct citations. Position: **NE — Not enough evidence**.

The project's measurements are historical: the committed evaluation ran on 2026-09-18 at `5be21d0`. The evaluation's retrieval step calls the search function directly with the fixed question set (`evals/run.ts:20-41`), and the retrieval code (`src/retrieval/`) and frozen evaluation corpus (`evals/corpus/`) are unchanged since then. So recall@10 still describes the reviewed code. Answer-level figures, such as citation mismatch counts and which questions failed, are historical only, because nine prompt and answer-formatting changes have merged since. The references report results only on public benchmarks with different data and tasks, so no comparable measurement connects the project to them. Whether their approaches would improve this corpus is a hypothesis for F1 to test, not a demonstrated gap.

References, verified against current sources during the review: **A**, an open-source hybrid retrieval and reranking library (release notes, 2026-08); **B**, a platform file-search feature with built-in citations (documentation, 2026-07); **C**, a published comparison of retrieval pipelines on a public question-answering benchmark (paper, 2026-05).

| Dimension | Project evidence | Strongest reference (source, date) | Gap |
|---|---|---|---|
| Retrieval quality | Dense-only retrieval; recall@10 of 0.71 in the historical run, still applicable because retrieval code and corpus are unchanged; retrieval misses behind most failed questions in that run (`src/retrieval/search.ts:14-66`, `evals/results/2026-09-18.json`) | A: hybrid lexical and dense retrieval with cross-encoder reranking (release notes, 2026-08); C reports recall gains from reranking on a public benchmark (paper, 2026-05) | Not comparable: different datasets; whether the gains transfer is F1's hypothesis |
| Citation accuracy | Current rate unmeasured. The historical run flagged 23 of 180 answers for citation mismatches, and the merge defect that produces such mismatches still exists (reproduced; R2) (`evals/results/2026-09-18.json`) | B: span-level citations built into file search (documentation, 2026-07) | Unmeasured now; the R2 defect persists |
| Evaluation rigor | 180-question set, run by hand (`evals/README.md:1-40`) | A and C gate changes on evaluation suites (2026-08, 2026-05) | See R3 |
| Model currency | Pinned to a model generation two releases old (`src/answer/client.ts:8`) | The same provider's current generation (provider documentation, 2026-07) | Older generation; effect on this task unmeasured |

Build versus adopt: `src/ingest/` largely rebuilds B's managed file search. Adopting B could retire that package, but it would move document content to the provider, which needs a data-handling decision from the owners; it is listed as an alternative below rather than as an opportunity.

### F1 — Test hybrid retrieval with reranking

- **Evidence:** No comparable gap exists. The project uses dense-only retrieval with a historical recall@10 of 0.71, which still applies to the unchanged retrieval code, and retrieval misses caused most failed questions in that run (`src/retrieval/search.ts:14-66`, `evals/results/2026-09-18.json`). References A and C use hybrid retrieval with reranking and report gains on public benchmarks, which motivates a transfer hypothesis.
- **Verification:** Plausible — the references justify the test, but neither the direction nor the size of any improvement on this corpus is established.
- **Hypothesis:** Adding lexical retrieval and a reranking stage behind a flag improves recall@10 on the project set over the re-measured baseline (historically 0.71), reaching at least 0.80 with p95 latency under 3 seconds.
- **Decisive experiment:** First re-run the full evaluation at the reviewed commit to establish current recall@10 and answer accuracy. Then compare hybrid retrieval with reranking on the same 180 questions. Time box: three days.
- **Success and stop criteria:** Build it if recall@10 reaches 0.80 and answer accuracy rises by at least five points over the re-measured baseline; stop if recall@10 improves by less than three points, including no improvement, or p95 latency exceeds 3 seconds.
- **Prerequisites:** R1 blocks the bet (a security D on the core answer workflow). Run it after R3 so the comparison is reproducible.
- **Status:** Blocked by R1.
- **Alternatives considered:** Upgrading the model generation is cheaper, but in the historical run retrieval misses, not reasoning, caused most failed questions; re-check that attribution when the baseline is re-measured. Adopting B's file search would retire code, but it needs a data-handling decision first.

## Category opportunities

### R1 — Stop retrieved documents from triggering the email tool

- **Primary category:** Security and privacy fundamentals
- **Verification:** Confirmed — reproduced with a throwaway test against the stubbed model client. A document containing send-email instructions lands in the system prompt, and the email tool executes with no confirmation step. Whether a live model follows the instruction was not tested; that requires credentials.
- **Evidence and consequence:** Retrieved text is concatenated into the system prompt without delimiters (`src/answer/prompt.ts:22-58`), and the always-registered email tool sends immediately (`src/answer/orchestrate.ts:88-104`, `src/tools/email.ts:10-44`). Anyone who can edit an indexed document, including in the HR and finance folders (`config/sources.yaml:3-21`), can attempt to make the assistant email retrieved content to an outside address.
- **Smallest useful change:** Pass retrieved text as delimited, lower-trust content, and require explicit user confirmation before the email tool sends. Non-goals: a general policy engine or an injection classifier.
- **Value versus cost:** High benefit; effort M, limited to prompt assembly and the email tool; users gain one confirmation step. The exposure exists today and the fix is bounded, so deferral is not justified.
- **Status:** Ready for ticketing (overall priority 1).
- **Acceptance evidence:** The reproduction now sends no email without confirmation; a user-initiated send still works after confirmation; existing answer tests pass.

### R2 — Keep citation offsets correct when chunks merge

- **Primary category:** Correctness and contract safety
- **Verification:** Confirmed — reproduced with a throwaway test. Merging two adjacent chunks keeps only the first chunk's offsets, so a quote from the second chunk links to the wrong passage (`src/retrieval/merge.ts:31-57`).
- **Evidence and consequence:** The historical 2026-09-18 evaluation run flagged 23 of 180 answers for citation mismatches (`evals/results/2026-09-18.json`). The current rate is unmeasured, but the reproduction shows the defect persists at the reviewed commit. Users who check a citation land on unrelated text, which undermines the product's main trust signal.
- **Smallest useful change:** Carry each source chunk's offsets through the merge, and map a citation to the span that contains the quoted text. Non-goals: re-chunking the corpus or changing the citation format.
- **Value versus cost:** High benefit; effort S, one merge function and its tests; low regression risk given the existing retrieval tests and the new reproduction.
- **Status:** Ready for ticketing (overall priority 2).
- **Acceptance evidence:** The reproduction passes; an evaluation run at the fixed commit reports no citation mismatches caused by merged chunks; existing retrieval tests pass.

### R3 — Run answer-quality evaluations on prompt and retrieval changes

- **Primary category:** Test quality and behavior protection (cross-referenced from automated checks)
- **Verification:** Confirmed — traced. The evaluation set and runner exist (`evals/README.md:1-40`), no CI workflow invokes them (`.github/workflows/ci.yml:1-64`), and nine prompt and answer-formatting changes have merged since the last committed result (`git log 5be21d0..91c0e4b -- src/answer/`).
- **Evidence and consequence:** Answer-quality regressions can merge unnoticed, the only results describe an older revision, and there is no reproducible baseline for judging F1.
- **Smallest useful change:** A CI job that runs the existing set on pull requests touching `src/answer/` or `src/retrieval/`, reports the score change, and fails only on a regression beyond an agreed tolerance. Non-goals: expanding the question set.
- **Value versus cost:** Medium-to-high benefit; effort S, one CI job using the existing runner. It adds model cost on each qualifying pull request and needs a CI secret.
- **Status:** Ready for ticketing (overall priority 3); the CI secret needs owner approval.
- **Acceptance evidence:** A pull request that changes a prompt shows its evaluation delta; a deliberate regression fails the check; unrelated pull requests are unaffected.

### R4 — Resolve the citation promise for answers without sources

- **Primary category:** Documentation accuracy and behavioral alignment
- **Verification:** Confirmed — traced. `README.md:5-9` says every answer cites its sources; `src/answer/orchestrate.ts:150-171` returns an uncited answer when retrieval finds nothing.
- **Evidence and consequence:** This is an unresolved contract conflict, not yet a documentation fix: either uncited answers should be refused, or the README overstates the guarantee. Users cannot currently tell a grounded answer from an ungrounded one.
- **Smallest useful change:** Decide the intended behavior once R2 changes citation handling, then correct whichever side is wrong. Non-goals: rewriting the README.
- **Value versus cost:** Medium benefit; effort S once decided.
- **Status:** Parked — outside the ticket budget; decide after R2.
- **Acceptance evidence:** The README and the orchestrator agree, and a test covers the empty-retrieval case.

### R5 — Verify whether anything still reads the legacy embedding index

- **Primary category:** Dead code and migration hygiene
- **Verification:** Plausible — no reader exists in `src/` or `scripts/`, but consumers outside the repository could not be checked.
- **Evidence and consequence:** The recorded migration moved retrieval to the current pipeline (`docs/adr/0007-embedding-pipeline.md:1-36`), but the legacy job (`infra/schedule.yaml:12-19`, `scripts/embed-legacy.ts:1-88`) still re-embeds the full corpus every night, with no recorded retirement condition. If nothing reads its index, that spend buys nothing.
- **Smallest useful change:** A bounded verification: check index access logs or ask the owning team before deciding on retirement. Non-goals: deleting the job in this step.
- **Value versus cost:** Medium benefit; effort S.
- **Status:** Parked — verification only, outside the ticket budget.
- **Acceptance evidence:** A recorded answer on whether any consumer reads the index, with the evidence used.

## Overall priorities — the ticket budget

1. **R1** — Closes a confirmed path from indexed documents to outbound email, the highest-risk finding, with a bounded fix.
2. **R2** — Restores correct citations, the product's main trust signal, with a small reproduced fix.
3. **R3** — Makes answer quality measurable on every relevant change and provides the baseline F1 needs.

## Deliberately leave alone

- The answer orchestration function (`src/answer/orchestrate.ts:30-212`) is tempting to split, but R1 already changes its tool handling and no other change has shown real friction. Restructure it only if that work does.

## Next decision

Create the R1 ticket first. Run F1's experiment after R1 lands and R3 provides a reproducible baseline.
