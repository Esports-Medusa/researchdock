# Phase 01: M0 + AI selection experiment + M1

The phase ends at a reproducible minimal statistics container, not a platform. Initialization completes only part of preparation. [STATUS](../STATUS.md) is authoritative for current progress; [PLAN](../PLAN.md) owns milestone scope. Estimated total is 30–40 active hours including learning: M0 8–10 + separate evaluation 6–10 + M1 16–20. Individual estimates below subdivide that total; they are assumptions.

## Checklist with stable IDs

### P1-01 — Initialize documentation and inspect environment (8–10 h, M0)

- [ ] Complete M0 preparation. **Partial:** documentation/skeleton and read-only inspection were performed; workflow and scope review remain.
- Purpose: establish boundaries and an honest evidence trail.
- Inputs: user requirements, dedicated directory, applicable instructions and existing Git state.
- Actions: inspect safely; initialize documents and Git if appropriate; check required tools without installing; review scope/invariants and confirm the working procedure.
- Expected files: README, AGENTS, PLAN, this phase, STATUS, ADRs, ENVIRONMENT, DEVELOPMENT_LOG, evaluation docs and directory READMEs.
- Acceptance: required files/local links/static checks pass; no unsupported completion claims; remaining tool/workflow questions explicitly recorded.
- Dependencies: none; dedicated path must be confirmed.

### P1-02 — Confirm candidates and protocol (0.5–1 h, evaluation)

- [ ] Verify available AI configurations and review a common protocol.
- Purpose: make the comparison attributable and affordable.
- Inputs: actual UI/CLI model selectors/version displays and existing account access; [PROTOCOL](../ai-evaluation/PROTOCOL.md).
- Actions: capture actual model ID/alias, reasoning configuration, tool version and availability; Unknown if invisible. Choose at most A/B without buying subscriptions. Classify model vs tool+model workflow comparison; if only A exists, plan a single-candidate suitability check.
- Expected files: sanitized candidate registry in [RESULTS](../ai-evaluation/RESULTS.md), ADR-001 update if needed.
- Acceptance: configurations and restrictions explicit, budget/time/rubric agreed and no brand-based winner; no runs yet.
- Dependencies: P1-01 documentation baseline.

### P1-03 — Prepare, self-check and freeze neutral benchmarks (2–3 h, evaluation)

- [ ] Prepare B1–B3 packages, synthetic fixtures, references and independent acceptance programs.
- Purpose: ensure the fixed tests evaluate the contract before a candidate generates code.
- Inputs: [TASKS](../ai-evaluation/TASKS.md), [benchmark rules](../../benchmarks/README.md), uniform environment/dependency choices.
- Actions: implement neutral seeds; verify B1 reference CLI and permission expectations; build B2 reference API and isolated PostgreSQL setup; prove B3 defective seed fails deterministic contention and reference passes. Self-check invalid-input, permissions and concurrency tests. Keep evaluator/reference outside candidate access. Review fixtures and freeze seeds, prompts, evaluator, rubric and dependencies with SHA-256 manifest. Optional user review reduces preparer bias.
- Expected files: future `benchmarks/seeds/`, `benchmarks/fixtures/`, `benchmarks/contracts/`, reviewed freeze manifest; private references/evaluators under `.local/ai-eval-preparation/`. These do not exist at initialization.
- Acceptance: reference checks actually pass; B3 negative control actually fails for the intended reason; dependency/schema isolation works; immutable pre-generation hashes recorded; no reference leaks. If prerequisites fail, mark Blocked and do not announce ready benchmarks.
- Dependencies: P1-02; authorized Docker engine for B1 self-check and PostgreSQL for B2/B3.

### P1-04 — Run candidates sequentially in isolation (2–3 h, evaluation)

- [ ] Execute one fresh session per candidate/task with preserved first output and at most one repair.
- Purpose: gather comparable observed evidence.
- Inputs: frozen packages and configurations from P1-02/P1-03.
- Actions: copy the same read-only snapshot to `.local/ai-eval/<candidate>/<task>/<run>/`; restrict session access; run sequentially via user-selected candidate settings. Budget 20 minutes first + at most 10 minutes repair per task, identical for all; repair receives real failure output only. Preserve first code and evaluation before repair; separate dependency setup time. Capture environment blockers and leakage limitations.
- Expected files: real local `runs/<run_id>/` records, isolated sandboxes, sanitized rows in RESULTS; no fake records if not executed.
- Acceptance: timestamps, commands/exit codes, hashes, first/repair outputs, interventions and limitations present; fixed evaluator unchanged; environment failures handled symmetrically.
- Dependencies: P1-03 frozen and verified; runtime prerequisites available.

### P1-05 — Score and record a decision (1.5–3 h, evaluation)

- [ ] Compare observed results and update ADR-001.
- Purpose: choose only on the prespecified evidence.
- Inputs: frozen rubric, valid runs, intervention and visible cost records.
- Actions: report first/final scores separately, three-task equal mean, mandatory gates and limits; use >=80 plus all critical gates for suitability; <=5-point difference is near tie. Repeat only disputed tasks symmetrically if allowance permits. If evidence is insufficient or neither qualifies, retain provisional use with smaller work units and human review.
- Expected files: RESULTS summaries, ADR-001 evidence/status update, DEVELOPMENT_LOG and changed STATUS.
- Acceptance: no winner from unavailable data; costs Unknown if invisible; preparer involvement and low sample count disclosed.
- Dependencies: P1-04. A single candidate supports suitability only, not superiority.

### P1-06 — Review and integrate the minimal statistics task (6–8 h, M1)

- [ ] Integrate a reviewed implementation into the project.
- Purpose: turn useful experimental learning into an auditable M1 component.
- Inputs: B1 evidence, trusted reference CLI, ADR-001; provisional workflow is acceptable if explicitly recorded.
- Actions: human-review code/licenses/dependencies/error handling and project fit. Implement CSV `(group,value)` to `summary.json` and `summary.csv`, with group count/mean/min/max, deterministic sorting/formatting and documented errors. Add meaningful project fixtures/tests; keep baseline reference separate from candidate ranking. Do not copy B2/B3 into the platform by assumption.
- Expected files: actual task implementation under `worker/` (final location recorded when implemented), reviewed synthetic fixtures in `tests/` or `examples/`, documentation.
- Acceptance: clean-output runs reproduce expected artifacts; input hash unchanged; malformed header/non-numeric/empty input exits nonzero without success-looking outputs; artifact schema and numbers checked.
- Dependencies: P1-05 or recorded evidence gap and provisional workflow. Evaluation code remains experimental until reviewed.

### P1-07 — Validate container execution and reproduction (8–10 h, M1)

- [ ] Complete real container success/failure and resource checks.
- Purpose: establish repeatable execution, not merely a plausible Dockerfile.
- Inputs: reviewed CLI/fixtures, actual container engine and recorded compatible image/runtime versions.
- Actions: build nonroot image; mount input read-only and persistent output writable with explicit UID/permissions. Run CPU/memory limits; inspect configured limits and verify resource behavior with a bounded probe. Validate exit codes, invalid-input cleanup, input hashes and persistence after container removal. Document exact executed commands and prerequisites in README.
- Expected files: real Dockerfile, run instructions, checks and sanitized execution summary; raw evidence under runs.
- Acceptance: from a clean output directory, both artifacts validate; source is unchanged; invalid cases fail; no root process; resource configuration/enforcement evidence recorded; output survives removal. Without Docker, script/static checks may pass but container acceptance is **Blocked / Not run**, and M1 remains incomplete.
- Dependencies: P1-06 and container prerequisites. No fake Compose launch flow.

### P1-08 — Close M1 with evidence (2 h, M1)

- [ ] Review gates, record limits and update status/logs.
- Purpose: establish a defensible M1 completion boundary.
- Inputs: run records and acceptance checklist from P1-06/P1-07.
- Actions: inspect reproducibility from documented commands, link failures and fixes, update STATUS only to supported completion; retain ADR history.
- Expected files: DEVELOPMENT_LOG, STATUS, reviewed README and artifact/evidence references.
- Acceptance: all M1 gates backed by execution evidence; unresolved container blocks prevent completion; no fabricated release tags or claims about M2/M3.
- Dependencies: P1-07 runtime acceptance.

## Scope guard

The chart-rendering template is added later by M3. B2's small submission API and B3's claim reproducer are isolated experiments; they do not implement the formal M2/M3 API/Worker, production leases, authentication lifecycle or network-partition behavior. M1 completion does not imply any formal platform service is running.
