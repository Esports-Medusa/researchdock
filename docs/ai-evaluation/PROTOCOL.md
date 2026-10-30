# AI-assisted development evaluation protocol

Status: documented protocol; experiments **Not run**. The rubric is a project-internal heuristic, not an industry standard. [TASKS](TASKS.md) defines fixed acceptance; [RESULTS](RESULTS.md) contains observations only. ADR ownership is [ADR-001](../DECISIONS.md#adr-001).

## Candidates and attribution

Default maximum: two candidates. A is the user's actually available current Codex coding configuration; B is another available model/reasoning setting in the same tool or a coding tool/model already accessible to the user. Do not buy subscriptions. Read exact ID/alias, reasoning setting and tool version from actual UI/CLI evidence; invisible information is Unknown, including account availability. The current assistant's self-description is not model evidence.

Same tool, permissions, system setup and environment with only model changed is a **model comparison** (if reasoning settings also differ, disclose the configuration confound). Different tools, system prompts or edit mechanisms make this a **tool+model workflow comparison**; do not attribute all differences to the model. One candidate permits a suitability check only; ADR-001 must say `provisional, no comparative evidence`.

The initialization assistant authored these specifications and could later help prepare seeds; disclose that involvement in each report. Reference validation, user review and new isolated sessions reduce bias but do not make the experiment fully independent.

## Freeze before generation

1. Prepare complete neutral packages and exact I/O contracts per TASKS, using synthetic fixtures. Select and record real compatible versions rather than fake locks.
2. Validate fixed evaluators against separate passing reference implementations; B3 must also demonstrate that the defective seed fails for its intended race. Keep reference answers inaccessible to candidates.
3. Freeze the same seed snapshot, fixtures, exact substituted task prompt, fixed evaluator, critical-gate list, rubric, dependency/runtime manifest and resource limits with SHA-256 hashes. Store immutable originals and hashes; no automatic Git commit is needed. A hash manifest is not proof of execution; keep self-check evidence separately.
4. Each candidate/task/run gets a fresh copy under `.local/ai-eval/<candidate>/<task>/<run>/`. The original is read-only and never reused after modification. Writable implementation allowlists are defined in TASKS; no shared modified implementation.
5. Each task uses a separate new session. Expose only its sandbox and necessary public contract/instructions, never project-root rankings, other implementations, logs or private references. A path alone is not isolation: use actual tool workspace restrictions. If unavailable, record the limitation and do not claim strict blindness. Keep evaluators and secret/reference results outside the allowed workspace.

Freeze changes after a defect is discovered require a new manifest/version and symmetric affected reruns, preserving previous evidence. Candidate-added tests cannot replace the fixed independent evaluator.

## Uniform execution conditions

Use the same OS/architecture, CPU/memory allocation, real Docker engine for B1, PostgreSQL version/temporary instance for B2/B3, dependencies, network/tool permissions, cache state and prefetch policy. Use separate candidate/task PostgreSQL databases or schemas with restricted credentials, never SQLite. Record database reset and connection settings without passwords. Host shared use/noise is a limitation.

Pre-download the same permitted dependencies/images where possible; separate setup/download/build preparation from timed task processing. Candidate-authored builds still belong to candidate processing time; common image/dependency prefetch is setup. Record both wall time and human active effort. Do not silently exclude slow attempts or dependency failures.

Run sequentially with the user switching candidate configuration. For each task: first attempt up to **20 minutes**; preserve code, output and fixed-test results immediately. Allow at most **one repair round, 10 minutes**, with the exact real failure output only, the same policy for all candidates. Capture deadline handling and any unvalidated partial output. Keep first and repaired snapshots separate; never overwrite first evidence. Record all human prompts and manual edits; hints beyond the policy invalidate comparability and require disclosure or rerun.

Classify `Completed`, `Incomplete`, `Timed out`, `Environment blocked` or `Tool failure` separately. Environment failures are not automatically capability failures. Repair environment then rerun every affected candidate equally, keeping old evidence. Each task normally has one trial per candidate: this is a low-sample experiment. Only disputed tasks may receive symmetric repeats; report every trial and use the arithmetic mean of valid repeated scores per task, with no best-run cherry-picking.

Raw session/logs stay ignored locally; only sanitized summaries are tracked. Token use and actual cost are recorded only from visible tool/account statistics; otherwise Unknown. Never infer cost from chat length or use unchecked prices.

## Fixed critical gates

Every item in this table is mandatory for final suitability. Map IDs to evaluator assertions before freeze; do not select gates after results.

| Task | Critical gates |
| --- | --- |
| B1 | B1-K1 normal JSON/CSV correctness and deterministic reproduction; B1-K2 each malformed case (missing value, nonnumeric, empty) nonzero with no success-looking artifacts; B1-K3 read-only input and unchanged hash; B1-K4 nonroot execution with writable persistent outputs surviving container removal; B1-K5 executed CPU/memory-limit evidence |
| B2 | B2-K1 legal submission starts QUEUED and GET contract holds; B2-K2 all disallowed parameters/resources rejected; B2-K3 same-user same-key same-content returns original and changed-content conflicts; B2-K4 same key across users isolated and foreign GET denied; B2-K5 synchronized concurrent duplicate submissions create exactly one job in real PostgreSQL |
| B3 | B3-K1 independent processes/connections collide under a deterministic barrier; B3-K2 corrected atomic claim gives each attempt at most one legal claimant without losing eligible work; B3-K3 error path rolls back/releases transaction locks and later claim succeeds; B3-K4 meaningful regression fails against defective seed and passes against fix without disabling concurrency |

Static inspection does not pass a runtime gate. Missing prerequisites leave affected gates Not run/Blocked, without pretending model failure or readiness. The no-exactly-once boundary still applies to all results.

## Scoring rubric (100 points per task)

Score first and final separately with written evidence per dimension. Intermediate values interpolate only within the anchored behavior and must name the passed assertions or observed review facts. Tests with no executed evidence receive no awarded correctness points; environment-blocked runs are marked unscored rather than zero for model ranking.

| Dimension | Zero | Partial | Full |
| --- | --- | --- | --- |
| Correctness and fixed acceptance (40) | No applicable fixed assertion passes, or no usable implementation | `40 * passed / total` fixed assertions; assertion IDs and weights frozen equally before generation | 40: all fixed assertions pass in the required runtime |
| Edge cases and data/permission safety (20) | No safety obligation evidenced, or severe input/ownership/data boundary violation | 5 points per evidenced checklist item: (1) all invalid-input/error cases cleanly handled, (2) input/data/identity boundaries enforced, (3) relevant concurrency/resource/isolation guard works, (4) no success-looking invalid/partial result or unsafe fallback | 20: all four; map task-specific evidence to each before freeze |
| Simplicity, maintainability and reproducibility (15) | No reproducible solution or wholly opaque/broken changes | 5 points each: (1) focused allowed changes/no unnecessary services, (2) clear implementation and dependency rationale, (3) exact reproducible instructions/compatible recorded prerequisites | 15: all three supported by code review and executed instructions |
| Diagnosis, explanation and tests (15) | Misdiagnoses core behavior and no useful verification | 5 points each: (1) correct causal explanation, (2) meaningful candidate regression/verification beyond a happy-path assertion, (3) honest commands/results/limitations | 15: all three; candidate tests supplement fixed acceptance only |
| Human intervention cost (10) | Manual implementation rescue, extra hints or repeated corrective intervention beyond one permitted round (also review validity) | 5: exactly one allowed failure-output repair prompt, no manual code edits | 10: first attempt needs no corrective human prompt or manual edit |

Task-specific safety mapping: B1 = invalid CSV cleanup / read-only input / nonroot+limits / atomic clean artifact handling; B2 = invalid-field rejection / trusted identity+ownership / database-backed concurrent uniqueness / rollback without partial job; B3 = controlled error handling / no lost or double-claimed data / database atomicity across processes / rollback and truthful claimant result. Freeze concrete assertion IDs before execution. These review dimensions intentionally include safety quality as well as functional checks; time/token/cost are never added to the quality score.

Initial task prompt, standard evaluator execution and passive review are not corrective interventions. Record their human time separately. Any bespoke coaching, even if helpful, is not the permitted repair format.

An invalid run is labeled **Invalid**, excluded with a reason, not quietly scored: tampering with fixed acceptance/reference results, fabricating passes, reading other candidates or editing outside sandbox. Repeat fairly only after the cause is resolved and disclosed.

## Selection rule

Compute each candidate's three-task equal-weight mean for first scores and final scores separately. A task without repair uses its first score as final. A partially blocked suite has no complete overall score or winner. A suitable primary candidate needs **all critical gates finally passed** and **final mean >=80**. A single-candidate passing suite permits provisional suitability, not comparative superiority.

If both qualify and the mean difference is **<=5**, call it approximately tied; prefer existing access, then lower recorded human intervention, then lower actually visible cost. Unknown cost cannot decide a winner. If still tied, retain the current accessible workflow provisionally. If only one qualifies, it is the evidence-supported primary candidate for these tasks, subject to disclosed limits. If neither qualifies, record gaps, reduce delegated task size or add learning; do not force a winner. Never replace the initial results with repaired scores. Revisit when model/tool/configuration changes, critical failures appear or workload changes.
