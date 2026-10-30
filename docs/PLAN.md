# ResearchDock overall plan

This is the single overall plan. Execution detail belongs in [Phase 01](phases/PHASE_01.md); actual progress belongs in [STATUS](STATUS.md). All milestone names, version names and hours below are planning assumptions, not delivered features or commitments.

## Problem and users

Small research teams need a consistent way to submit repeatable CPU work across a local Linux node and one public-cloud node, understand capacity and failures, and recover outputs without administering each command manually. ResearchDock is a learning and portfolio platform for junior DevOps, developer productivity, cloud operations and research-computing support. FigColor covers the separate full-stack/AI application portfolio role. This project borrows research-computing scenarios without claiming to reproduce university BEAR, enterprise HPC, or a complete private cloud. A local VM is a local node.

User stories:

- As a researcher, I select a trusted template/version, a registered dataset, allowlisted parameters, local/cloud and CPU/memory/time limits; I receive a stable submission identity.
- As a researcher, I see queue position/status/logs, request cancellation, and download only my validated outputs.
- As a second ordinary user, I cannot read, cancel or download another user's jobs, even by guessing IDs or reusing their idempotency key.
- As an administrator, I register/drain nodes, reserve control-service resources, release and roll back versions, inspect incidents and restore data with evidence.

## Fixed scope

V1 includes two trusted fixed CPU templates: synthetic-data statistics and batch scientific-chart rendering. Use synthetic or public data only. Include queue/capacity checks, per-user concurrency, logs, persistent results, cancellation, timeouts, local/cloud selection, pull Workers and heartbeats, researcher/admin roles, reproducible configuration, CI/CD, monitoring, backup/restore and fault exercises. Test object permissions with at least two ordinary identities plus admin.

Exclude arbitrary user code/images, GPU, multi-cloud, autoscaling, complex scheduling, distributed training, AI operations assistants, a complete private cloud and control-plane high availability. Do not introduce Kubernetes, Slurm, Redis or a message queue in V1.

## Components and deployment boundaries

| Component | Responsibility and boundary |
| --- | --- |
| Vue 3 web | Small submission/status/log/result/admin interface; server enforces access |
| Python/FastAPI control API | Validation, authentication/authorization, idempotency, resource policy and artifact access |
| PostgreSQL | Durable jobs/attempts/node registrations/leases; task-state authority and atomic claims |
| Python Worker | Pull eligible work, heartbeat, enforce Docker limits, stop/verify containers, stage and validate artifacts |
| Trusted task images | Fixed versioned CPU algorithms, explicit input/output/error contracts; no arbitrary images |
| Artifact storage | Persistent job/attempt-isolated logs and outputs, manifest/checksum validation before success |
| Infrastructure | Docker/Compose, Terraform, Ansible and WireGuard; GitLab CI/CD; Prometheus/Grafana |

M0–M3 use local development. M4 adds one public cloud; Alibaba Cloud is a preferred evaluation candidate only. No provider purchase, region or specification is approved. Initial cloud control services and cloud Worker may share a host with explicit reserved CPU/memory for control services; keep the local Worker. PostgreSQL must not be publicly exposed. Worker/control networking must authenticate nodes and restrict peers; WireGuard is the planned private interconnect. Laptop sleep makes its local node offline.

The provisional ceiling is **CNY 200–300/month**, a budget constraint rather than a quote. Before purchase, estimate compute, disk, traffic, public IP and backup costs against actual current offerings; leave cloud blocked if they do not fit. No cloud resources are authorized by initialization.

Versions are intentionally unpinned here. Confirm stable compatible versions and record provenance at first implementation; generate genuine dependency locks only from actual resolution. Prefer the minimal PostgreSQL-backed queue to reduce infrastructure, while accepting responsibility for transaction, contention and lease correctness. Review [ADR-003](DECISIONS.md#adr-003).

## Data and lifecycle invariants

Planned records: user, template/version, registered dataset, job, independent attempt, node/capacity, lease/heartbeat and artifact manifest. Submission records a canonical allowlisted request and a user-scoped idempotency key. A unique database constraint covers `(user_id, idempotency_key)`; concurrent same-content submissions return the original job, different content returns a conflict. Request bodies cannot choose their trusted identity.

- A valid submission begins QUEUED. Invalid resource/template/dataset/parameter requests are rejected.
- Within each requested environment, strict FIFO is the default. The oldest eligible queued job waits if capacity or its user's concurrency prevents dispatch; do not silently skip it for younger work. Local and cloud have separate eligibility; never silently migrate local work to cloud. Policy changes need an ADR and tests.
- Atomically claim QUEUED work and create a new independent attempt in a transaction after environment/capacity/concurrency checks; mark RUNNING with an ownership token and lease. Concurrency/capacity reservations must be transactional to prevent over-allocation.
- Workers heartbeat leases and nodes. Lease/heartbeat loss moves unresolved execution to UNKNOWN for reconciliation, not automatic failure or retry. Inspect the old container, ownership and artifacts before resolving; quarantine uncertain capacity until reconciled.
- Normal execution can yield SUCCEEDED or FAILED. Success requires durable outputs and checksum/schema verification. A timeout requires stopping and confirming the process/container exited before TIMED_OUT; inability to confirm termination becomes UNKNOWN.
- QUEUED cancellation can become CANCELLED atomically without a launch. Running cancellation becomes CANCEL_REQUESTED; only confirmed container exit permits CANCELLED. Handle launch/cancel and completion/cancel races using attempt ownership and recorded transition evidence.
- An expired or superseded attempt cannot submit terminal state. Fence every state/artifact publication by current attempt ownership; isolate all output paths as `job_id/attempt_id`. Preserve rejected stale reports for diagnosis.
- Manual retry creates a new attempt after reconciliation of the previous execution; never reuse output paths or allow overlapping unresolved attempts. Automatic retries may be deferred. No exactly-once claim is made.

The state vocabulary is QUEUED, RUNNING, SUCCEEDED, FAILED, TIMED_OUT, CANCEL_REQUESTED, CANCELLED, UNKNOWN. Transition guards, terminal-state immutability and UNKNOWN-resolution permissions require explicit M2/M3 tests. Logs and artifacts carry the same object permissions as jobs. Trusted image/resource settings are selected by the service, not shell fragments from requests.

## Milestones and dependencies

Each row's hours includes learning and implementation. Dependencies are sequential; evaluation adds separate work after initial M0 preparation.

| Milestone | Goal and main work | Dependency | Acceptance and deliverables | Learning focus | Deferrable if late | Hours |
| --- | --- | --- | --- | --- | --- | --- |
| M0 | Freeze requirements; inspect environment; establish directory, evidence and workflow rules | Dedicated directory and user scope | Documents/links checked, boundaries explicit, usable tool workflow confirmed; plans, ADRs, environment and log | Shell, Git hygiene, scope and evidence | Cosmetic documentation polish | 8–10 |
| M1 | Reproducible statistics CPU container; deterministic CSV-to-summary CLI, validated mounts/limits/errors | M0; evaluated or explicitly provisional AI workflow; reviewed integration | Clean-output reproduction, unchanged input, invalid-input nonzero, validated artifacts, nonroot container and recorded resource checks; CLI, image definition, fixtures and run instructions | Linux permissions, Docker, Python I/O | Chart template and image optimization; never core acceptance | 16–20 |
| M2 | Local API–PostgreSQL–Worker loop; schema, atomic claims, attempts, leases and artifact publishing | M1 | Submit/claim/execute/fetch tested; contention, idempotency and stale-attempt fencing checked; migrations, API/Worker, integration tests | API contracts, SQL transactions and state machines | Convenience API filters and automatic retries | 26–32 |
| M3 | Demonstrable MVP, planned v0.1; roles, object permissions, quotas, cancel/timeout/UNKNOWN reconciliation and light UI; add chart template | M2 | Two ordinary users + admin exercise access matrix; resource/concurrency limits, cancellation exit confirmation, timeout and disconnected-node recovery tested; two templates and demo script | Auth, failure semantics, focused Vue UX | UI styling, advanced log search; keep all reliability/permission gates | 26–32 |
| M4 | Hybrid local + one-cloud deployment with IaC, config management and private networking; planned v0.2 | M3 and explicit cloud choice/budget approval | Rebuildable node setup; local/cloud jobs stay in selected environment; DB private; cloud control resources reserved; IaC/config/network evidence and cost estimate | Cloud networking, Terraform, Ansible, WireGuard | Shared cloud host remains acceptable; extra environments | 22–28 |
| M5 | Automated delivery, health checks and version rollback | M4 | GitLab pipeline tests/builds/deploys with protected secrets; deliberate bad release detected and rollback demonstrated; pipeline and release runbook | CI/CD, health semantics and artifact versions | Sophisticated promotion UI; keep rollback | 14–18 |
| M6 | Monitoring/alerts, backups/restores and fault exercises | M5 | Observe queue/attempt/node health; trigger an alert; restore DB and artifact consistency; exercise lost node, full disk and interrupted release; measured recovery report | Prometheus/Grafana, backup integrity, incident response | Extra dashboards and alert channels; keep a real restore | 20–26 |
| M7 | Traceable portfolio delivery; planned v1.0 | M6 | Reviewed docs, architecture, report, recording, limitations and evidence-linked resume bullets; no unsupported production claims | Technical communication and evidence synthesis | Video polish and decorative diagrams | 8–14 |

Base total: **140–180 hours**, retaining the original learning+implementation estimate. The separate AI comparison allowance is **6–10 hours**, including benchmark preparation, review, sequential attempts, scoring and evidence. Combined scope: **146–190 hours**, approximately **12.2–19 weeks** at 10–12 hours/week (plan roughly **13–19 whole weeks**). At 10 hours/week the range is 14.6–19 weeks; at 12 it is 12.2–15.8. Two candidates × three tasks × 20+10 minutes is at most 3 hours of timed candidate attempts before preparation and review. Optional symmetric repeats consume remaining allowance or require explicit replanning; do not hide them in M1 hours. Phase 01 uses M0+M1 (24–30) plus evaluation (6–10), hence **30–40 hours**, about 2.5–4 weeks. No released tags are created by this plan.

## Learning route: enough to proceed

| Dependency | ResearchDock experiment | Continue when |
| --- | --- | --- |
| Linux and networking | Inspect files/permissions/process exit; explain local ports and node-offline behavior | Can diagnose permission vs process vs network failure without exposing secrets |
| Containers | Run statistics with read-only input, persistent output, nonroot user and CPU/memory cap | Can reproduce success/error, explain mounts and prove configured/effective limits |
| Python automation and API | Build deterministic CLI then validate submission DTOs | Can test malformed inputs and stable output contracts without shell interpolation |
| Transactions, state machines, idempotency | Competing PostgreSQL claims and duplicate POSTs | Can explain race interleavings, unique constraints, rollback, leases and fencing |
| Permissions | User A/B/admin access matrix over jobs, logs, cancel and downloads | Cannot retrieve another user's objects through guessed IDs or keys |
| Cloud networking and IaC | Recreate a private-connected cloud node from reviewed config | Can explain routes/firewalls/state and show DB not public within budget |
| CI/CD | Deploy a known artifact then introduce/revert a bad release | Can identify the tested artifact and recover previous service health |
| Observability and recovery | Lose a node, trigger alert, restore a backup | Can distinguish detection from recovery and report observed loss/recovery windows |

Learn at the dependency boundary; do not require finishing every course before building a small experiment.

## Risks and portfolio delivery

- Missing Docker/VM capacity can block M1 runtime acceptance; report script/static progress separately and arrange a suitable authorized Linux/container environment.
- Long-tail concurrency, lease and cancellation races can exceed estimates: keep reliability gates, simplify UI and retries first, record unresolved cases explicitly.
- Sleep/network partitions and cloud co-location reduce availability; UNKNOWN reconciliation and reserved control resources are required, not HA claims.
- Provider costs/permissions are unverified; review actual quotes and budget before M4, never treat the ceiling as a price.
- Model results are a low-sample internal heuristic. Tool differences and preparation bias must be disclosed; no winner without evidence.
- Secrets, real datasets and raw sessions must stay outside tracked evidence. Check tracked files as well as ignore patterns.

Deliver a reproducible task, architecture and ADR trail, permission/concurrency tests, IaC/pipeline examples, rollback/restore/fault reports, a short demonstration and evidence-linked resume statements. Report measured observations only after execution, including failures and limits. V1 readiness requires the milestone gates, not completion of documentation alone.
