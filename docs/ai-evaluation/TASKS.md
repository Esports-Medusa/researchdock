# Fixed benchmark task packages

**Specification only; Not run.** No seed CLI, fixture files, reference, database, evaluator or frozen package exists yet. Preparation is P1-03, not this initialization. The specifications below must become self-checked packages before any candidate runs. [PROTOCOL](PROTOCOL.md) owns timing, scoring, critical gates and isolation; [benchmark preparation](../../benchmarks/README.md) owns the freeze process.

Every task gets the same fresh seed per candidate, with actual dependencies and versions recorded, read-only public contracts, external fixed acceptance and inaccessible reference implementations. Substitute `<SANDBOX>` and `<RUN_ID>` with actual isolated values before freezing the exact submitted prompt. Give no candidate the project root or other results. If workspace restrictions are unavailable, disclose that limitation and do not claim strict blindness. A preparation failure must be fixed symmetrically before scoring.

## B1 — Containerize a deterministic statistics task

### Inputs supplied by the preparer

- `seed/stats_cli.py`: already verified standard-library-only Python algorithm. Candidates do not write or change the statistics algorithm.
- `contract/README.md`: exact I/O/exit/permission rules below, verified by reference runs.
- `fixtures/valid.csv`, `missing_value.csv`, `nonnumeric.csv`, `empty.csv` (header-only), and expected example `expected/summary.json`. Independent acceptance checks also validate CSV and repeatability.
- Same container engine, recorded compatible Python base image/version, CPU/memory allocation, network/cache policy and host UID/GID/output-directory preparation for every candidate.

Synthetic valid fixture (prepare as UTF-8 with LF):

```csv
group,value
alpha,1
alpha,3
beta,2
beta,6
```

Expected JSON semantic value (formatter/order fixed by the supplied CLI):

```json
{"groups":[{"group":"alpha","count":2,"mean":2.0,"min":1.0,"max":3.0},{"group":"beta","count":2,"mean":4.0,"min":2.0,"max":6.0}]}
```

### Frozen contract

Command inside image: `python /app/stats_cli.py --input /input/data.csv --output-dir /output`. Required columns are exactly `group,value`; reject missing/extra/duplicate columns, missing cells, empty groups, nonfinite or nonnumeric values, no rows and malformed CSV. Group values are nonempty strings; output groups sort lexicographically. The seed defines deterministic UTF-8/LF JSON/CSV formatting and numerical serialization. CSV header is `group,count,mean,min,max`, with one row per group. No pandas or candidate algorithm work is required.

Exit 0 on success, 2 on invalid input, 1 on I/O/internal failure. Errors go to stderr. Parse/validate before publishing final `summary.json` and `summary.csv`; no final success-looking files may be present on invalid input in a fresh output directory. Output directory must already exist, be empty and be writable; reject preexisting final outputs without deleting them. Intermediate writes must not be confused with committed results. The supplied CLI must be proven to implement these rules before freeze.

Container must run as a nonzero UID, with input bind-mounted read-only and an external persistent writable output mount. Preparer fixes the same nonroot UID/GID and host output ownership policy for all candidates; root or world-writable workaround is not acceptable. Engine may need platform-specific mapping, recorded identically. Use limits of 1 CPU and 256 MiB for the small fixture, verified in the chosen engine during preparation. A read-only root filesystem is optional; do not add untested hardening requirements after freeze.

### Candidate outputs and allowed files

Write only `Dockerfile`, `.dockerignore`, `RUN.md`, and optional `verify.sh` inside its sandbox. Seeds, fixtures, contracts and expected example are read-only. No production service or algorithm change. RUN.md must contain exact build/run/cleanup commands, mount/permission preparation, exit codes and the distinction between observed and unexecuted checks.

### Fixed acceptance

B1-K1: build and run valid input from clean outputs; check exact JSON schema/numbers, CSV fields and deterministic rerun. B1-K2: separately run missing-value-column, nonnumeric and header-only fixtures; require exit 2, stderr and no final summaries; evaluator additionally checks stated malformed/error contract. B1-K3: prove read-only mount and input SHA-256 unchanged. B1-K4: inspect nonzero running UID, writable output, and read results after removing container. B1-K5: inspect CPU/memory configuration and run a shared bounded resource probe to evidence effective limits, keeping raw results. Preparer defines the probe/acceptable engine evidence before freeze; Dockerfile text alone passes no runtime gate. Check preexisting output refusal and I/O failure exit 1. Reference self-check covers these cases before candidates.

### Independent candidate prompt

```text
You are running benchmark B1, not implementing the ResearchDock platform.
Your only workspace is <SANDBOX>; run ID is <RUN_ID>. Do not access any
other candidate, project-root results, private evaluator or reference code.
Only Dockerfile, .dockerignore, RUN.md and optional verify.sh may be changed.
Do not modify seed/, fixtures/, expected/, contract/, fixed tests or scoring.

The supplied verified standard-library Python CLI is seed/stats_cli.py.
Its container command is:
python /app/stats_cli.py --input /input/data.csv --output-dir /output
Input CSV columns are exactly group,value. It rejects malformed/duplicate/
extra/missing headers, missing cells, empty groups, nonfinite/nonnumeric
values and zero data rows. It emits summary.json {"groups":[...]} and
summary.csv with group,count,mean,min,max; groups sort lexicographically.
The valid fixture alpha=[1,3], beta=[2,6] has count=2 each, means=2/4,
mins=1/2 and maxes=3/6. Keep the supplied algorithm unchanged.
Exit codes: 0 success, 2 invalid input, 1 I/O/internal failure; stderr on
error. The output directory must exist, be empty and writable. Invalid input
must leave no success-looking final summaries; preexisting outputs must not
be overwritten. The read-only public contract defines exact serialization
and the common UID/GID/host output permissions and compatible image runtime.

Create a minimal image and exact reproducible build/run instructions. Run as
a nonroot UID, bind input read-only, persist output outside the container,
and document running with 1 CPU and 256 MiB. Do not use root/world-writable
permission workarounds. Demonstrate valid output, missing value column,
nonnumeric input, header-only input, unchanged input hash, refusal of existing
outputs, persistent results after container deletion, and observed resource
limits using the shared prepared checks. Add focused optional verification;
it does not replace the fixed evaluator. Distinguish executed checks from
static inspection and missing-engine blockers. Explain mount permissions,
exit behavior and limitations. Do not fabricate commands or passing results.

Use only the supplied dependency/network/tool permissions. First attempt:
20 minutes. Stop and preserve your first result at that deadline. At most
one 10-minute repair is allowed when given the real fixed-test failure output.
Do not start a repair yourself, install system software, buy services, alter
acceptance/reference results, or commit/push. End with changed files, actual
commands/results and remaining limitations.
```

## B2 — Minimal idempotent submission API

### Inputs supplied by the preparer

Same FastAPI scaffold, declared compatible dependencies, migrations/schema fixture, trusted user dependency and real temporary PostgreSQL instance, isolated candidate database/schema and credentials. Do not use SQLite for concurrency. The trusted dependency supplies synthetic `user-a` and `user-b`; test-only identity selection is confined to the fixture, never request-body identity. Authentication products and execution are out of scope.

Seed paths: `app/main.py`, read-only `app/identity.py`, `app/database.py`, and editable `app/jobs.py`, `app/models.py`, `app/schemas.py`, `migrations/001_jobs.sql`. If scaffolding needs additional paths, adjust and freeze the allowlist before any candidate starts. Candidates may also edit `tests/test_jobs_candidate.py` and `RUN.md`. Fixed external evaluator and read-only dependencies cannot be changed.

### Frozen request and response contract

`POST /jobs`, authenticated by supplied dependency, requires `Idempotency-Key` (1–128 printable ASCII characters, no blank/whitespace-only key), JSON body with **exactly**:

```json
{"template":"statistics","version":"v1","dataset_id":"synthetic-v1","parameters":{},"environment":"local","cpu":1,"memory_mb":256,"time_limit_s":60}
```

Allow template/version/dataset only as shown; parameters is the empty object for this experiment; environment local or cloud; integer CPU 1–2, memory 128–1024 MiB, time 1–300 seconds. Reject booleans masquerading as integers, coercion of strings to integers, extra fields (including user_id), missing fields and unregistered values. No capacity scheduler is implemented here.

First valid request returns 201; repeat by the same user/key and equivalent normalized JSON returns 200 with the **same** job. JSON key ordering is irrelevant; every accepted body field is part of identity. Changed content under the same user/key returns 409. Missing/invalid key or invalid body returns 422. Unauthenticated fixture identity returns 401.

Response fields exactly `id` (UUID string), `status` (QUEUED), and `request` (validated body). No worker exists, so no RUNNING/SUCCEEDED. No identity/key leakage. `GET /jobs/{id}` returns 200 with that representation for owner, 404 for foreign or nonexistent UUID, and 422 for malformed UUID (401 without identity). Distinct users may use the same key and get different IDs; neither sees the other's payload. Test with different valid payloads across users.

Use database-enforced uniqueness `(user_id, idempotency_key)` plus transaction-safe conflict handling and canonical comparison, not a read-then-insert check alone. Rollback errors without leaving partial rows or leaked failed transactions. A repeat after the response was lost must still retrieve the original.

### Outputs and fixed acceptance

Candidate implements only POST and GET through the supplied scaffold and permitted files; no executor, arbitrary identity header implementation, UI or external service. Submit RUN.md explaining transaction/constraint choices and executed tests. Evaluator covers all contract cases, at least two synchronized concurrent same-user/key requests on separate PostgreSQL connections (exactly one row, one 201, others 200, one ID), concurrent different-content conflicts, identity isolation/GET denial and post-error recovery. B2-K1–K5 map to the [critical gates](PROTOCOL.md#fixed-critical-gates). Record actual SQL/reset/connection evidence without credentials. Do not replace PostgreSQL with mocks/in-memory locks.

### Independent candidate prompt

```text
Implement benchmark B2 only in <SANDBOX>, run <RUN_ID>. This experimental
API is not the production ResearchDock M2/M3 implementation. Only change
app/jobs.py, app/models.py, app/schemas.py, migrations/001_jobs.sql,
tests/test_jobs_candidate.py and RUN.md. Keep the supplied main wiring,
identity/database dependencies, fixtures, package versions and fixed
acceptance unchanged. Do not access other candidates, project-root results,
private evaluator or reference answers.

Use the provided FastAPI scaffold, trusted user dependency and assigned
isolated database/schema on the common real PostgreSQL instance. Do not use
SQLite, mock persistence, an in-memory mutex or request-body identity.
Implement only POST /jobs and GET /jobs/{id}; there is no executor.
POST requires Idempotency-Key: 1-128 printable ASCII characters, nonblank.
The exact body fields are template="statistics", version="v1",
dataset_id="synthetic-v1", parameters={}, environment="local" or "cloud",
cpu=integer 1..2, memory_mb=integer 128..1024, time_limit_s=integer 1..300.
Reject missing/extra fields, user_id, bool/string integer coercion and
unregistered values with 422; missing/invalid key also gives 422.
Trusted unauthenticated fixture requests give 401.

First valid submission returns 201 with exactly id (UUID string),
status="QUEUED", request (validated body). Same user and same key with the
same accepted content, ignoring JSON key ordering, returns 200 and the
original representation. Same user/key with different content gives 409.
All accepted body fields affect equivalence. Different users may reuse a
key without sharing job IDs or data. GET returns the same representation
for the owner (200), 404 for foreign/nonexistent UUID, 422 for malformed
UUID, and 401 without identity. Never claim execution or return RUNNING or
SUCCEEDED. All contract details are in the read-only supplied package.

Handle concurrent repeats transactionally with PostgreSQL uniqueness on
(user_id,idempotency_key). Separate connections racing on the same content
must create exactly one row, return one 201 and remaining 200s with one ID;
racing different content must conflict appropriately. Ensure rollback and
subsequent request recovery. Add meaningful tests; candidate tests do not
replace the fixed evaluator. Explain the transaction interleaving and
identity boundary in RUN.md, with actual commands/results or clear blockers.

Keep common resource, dependency, network and tool permissions. First
attempt: 20 minutes; stop and preserve results. At most one 10-minute repair
is allowed only when given real failure output. Do not modify standards or
reference results, install system software, buy services, commit/push or
fabricate test passes. End with changed files and observed limitations.
```

## B3 — Diagnose and fix a double-claim race

### Inputs supplied by the preparer

- Editable `claim.py`: intentionally flawed select-QUEUED-then-update claimant on PostgreSQL, plus a fixed public contract and invocation adapter.
- Synthetic `jobs` and `attempts` fixture schema/reset, independent-process runner and synchronized race reproducer. The preparer must first prove the defective version fails; timing sleeps alone are not a reliable reproducer.
- Same dependency versions and PostgreSQL instance with isolated candidate schema/database, and read-only safe test hooks for synchronization and error injection.
- External evaluator with private reference fix; candidate may edit only `claim.py`, `tests/test_claim_regression.py`, and `DIAGNOSIS.md`.

Fixture contract: jobs have unique IDs, queued/running states and deterministic FIFO order; each available job has a unique queued attempt ID. `claim_next(conn, worker_id)` returns `None` if no claim is currently available, otherwise `{job_id, attempt_id, worker_id}` for a **committed** successful queued-to-running ownership transition. Read-only adapter fixes the actual signature and database driver before freeze. Each attempt may have only one legal successful claimant. Independent processes hold independent connections; no process-global lock is relevant. Atomic row locking/conditional updates/transactions may be used. Public rendezvous is before invoking claim, so corrected locking need not cross an in-transaction barrier that deadlocks; a seed-specific read hook proves the initial interleaving in the negative control.

Two phases of reproduction: force the flawed seed's select/update overlap with a seed diagnostic hook to demonstrate double claim; use a start barrier and repeated synchronized independent claimers to exercise the corrected public API. Include a multi-job drain phase so SKIP LOCKED-style temporary `None` is not mistaken for a lost job. Inject an exception after acquiring/modifying state and before commit through a common read-only hook; rollback must release locks and permit later successful claim. Do not catch and report success before commit.

### Outputs and fixed acceptance

Explain the exact interleaving and transaction-based correction in DIAGNOSIS.md; implement the atomic fix and meaningful candidate regression. Evaluator checks B3-K1 independent-process contention, B3-K2 one legal owner per attempt and no eligible job left after complete drain, B3-K3 injected failure releases transaction/locks and no partial state persists, B3-K4 regression detects the defective behavior and succeeds on correction. Preserve concurrency; do not serialize with in-memory locks, bypass hooks, weaken checks or run one worker. Define exact fixture counts/repetition schedule before freeze. The scope is atomic claiming only: no production leases, network partitions or exactly-once guarantee.

### Independent candidate prompt

```text
Diagnose and fix benchmark B3 in <SANDBOX>, run <RUN_ID>. This is an atomic
claim experiment, not a production lease/exactly-once implementation. Only
edit claim.py, tests/test_claim_regression.py and DIAGNOSIS.md. Public
contract, fixture/reset, synchronization/error hooks, runner, dependencies
and fixed acceptance are read-only. Do not access other candidates,
project-root rankings, private evaluator or reference implementation.

The supplied PostgreSQL claimant incorrectly reads a queued attempt and
then updates ownership nonatomically. Explain an interleaving in which two
independent processes/connections both report success. Fix it using real
database transaction/row-lock/atomic-update semantics. Do not replace
PostgreSQL, use a single-process memory lock, disable concurrency, skip
hooks or weaken tests.

Preserve the public claim_next(conn,worker_id) contract in the supplied
adapter: return None if nothing is currently claimable, otherwise committed
{job_id,attempt_id,worker_id} for the successful queued-to-running claim.
Each attempt has at most one legal successful owner. Use deterministic FIFO
fixture order. With two independent processes/connections released by the
provided start barrier, prevent duplicate ownership and drain all eligible
jobs; a temporary None while another row is locked must not lose work.
An injected exception before commit must roll back changes and release
transaction locks so a later independent claim can succeed. Never return
success before commit. The seed diagnostic hook demonstrates the original
select/update overlap; corrected transactions must not be forced through a
deadlocking in-transaction rendezvous. Preserve the fixed harness.

Add a meaningful regression that detects the flawed seed and passes against
the fix. In DIAGNOSIS.md explain the race, SQL/transaction remedy, rollback,
actual test commands/results, and limits. Candidate-added tests supplement
the independent evaluator; they cannot replace it. Do not claim production
lease safety, network-partition recovery or exactly-once execution.

Use the supplied isolated PostgreSQL schema/database and uniform permissions.
First attempt is at most 20 minutes; stop and preserve outputs. At most one
10-minute repair is allowed only when real failure output is supplied.
Do not install system software, purchase services, commit/push, modify
acceptance/reference results or fabricate passes. End with changed files,
observed results and unresolved issues.
```

## Preparation and reporting boundary

Before generation, self-check the references and negative controls, finalize file allowlists/driver hooks/fixture counts and freeze their exact hashes with the substituted prompts and scorecard. This preparation is not candidate execution. The current initialization assistant authored the package specifications; disclose any later seed/evaluator involvement too. Candidate B1 integration requires separate human review in P1-06; B2/B3 completion never counts as formal platform delivery.
