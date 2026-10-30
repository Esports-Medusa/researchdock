# Decision records

Decision statuses are exactly **Proposed / Provisional / Accepted / Superseded / Rejected**. Implementation status and validation evidence are separate fields. Preserve original reasoning and append dated status changes with evidence and the superseding ADR ID. Do not erase history or equate an Accepted requirement with an implemented component.

## ADR-001

- **Title:** AI-assisted development workflow and primary model selection
- **Status:** Provisional
- **Context:** Development needs a reliable, affordable assistant workflow, but actual candidate configurations and comparative results are unknown. The initialization assistant is not a winner by default.
- **Options:** One accessible configuration with suitability checks; at most two accessible configurations with model or tool+model workflow comparison; defer selection if evidence is insufficient.
- **Decision:** Adopt the documented evaluation protocol as the working procedure. Primary model selection is pending evaluation. Temporarily use the currently accessible session for bounded initialization only: **provisional, no comparative evidence**.
- **Rationale:** Controlled tasks and preserved failures are better evidence than model brands or self-description. Avoid subscription requirements and uncontrolled evaluation cost.
- **Consequences:** Confirm actual UI/CLI configurations; prepare/self-check/freeze benchmarks; run sequential fresh sessions; preserve first/repair results; disclose preparer involvement. Single-candidate checks cannot prove superiority.
- **Evidence:** [Protocol](ai-evaluation/PROTOCOL.md), [tasks](ai-evaluation/TASKS.md), [results: Not run](ai-evaluation/RESULTS.md), [Phase 01](phases/PHASE_01.md). No model scores or availability evidence yet.
- **Decision basis:** User-specified evaluation requirements; routine engineering choice to encode prompts and rubric now. Specific model/tool availability and primary choice remain pending observed evidence/user configuration; no purchase is approved.
- **Revisit trigger:** Candidate availability confirmed, complete valid results, model/tool/settings change, critical reliability failures, or changed workload.
- **Date:** 2026-10-30 (UTC+08:00)
- **Implementation status:** Protocol/task/result documents created; seeds/evaluators and runs not implemented.
- **Validation evidence:** Documentation review/static checks only, linked in [development log](DEVELOPMENT_LOG.md); runtime comparison Not run.

## ADR-002

- **Title:** Local-first, then hybrid deployment
- **Status:** Accepted
- **Context:** Learn reliable local execution before adding public-cloud costs and networking.
- **Options:** Cloud-first; local-only indefinitely; local M0–M3 followed by one-cloud M4.
- **Decision:** Follow M0–M3 local, then M4 hybrid with local Worker retained. Alibaba Cloud is the preferred evaluation candidate, not a purchased or final provider. Region/specification/payment are undecided. The provisional budget ceiling is CNY 200–300/month, not a price quote.
- **Rationale:** Stage operational complexity and preserve a useful local path. Initial cloud control/Worker co-location is allowed only with reserved control resources and private DB access.
- **Consequences:** Verify actual provider costs, permission and network choices before purchase; account for laptop sleep; do not call a local VM a private cloud. Initialization authorizes no cloud creation or spend.
- **Evidence:** [Deployment plan](PLAN.md#components-and-deployment-boundaries), [environment unknowns](ENVIRONMENT.md).
- **Decision basis:** User-specified route and budget constraint. Final provider/region/specification/purchase remain user decisions after evidence.
- **Revisit trigger:** M4 planning, budget mismatch, account restrictions or demonstrable networking limits.
- **Date:** 2026-10-30 (UTC+08:00)
- **Implementation status:** Planned only; no deployment or cloud resources.
- **Validation evidence:** Runtime Not run; actual quotes and cloud readiness Unknown.

## ADR-003

- **Title:** Minimal stack and PostgreSQL as task-state authority
- **Status:** Accepted
- **Context:** A small trusted-template platform needs concurrency correctness without excessive infrastructure.
- **Options:** PostgreSQL-backed state/queue; extra message broker/Redis; full scheduler/Kubernetes/Slurm.
- **Decision:** Use the planned Python/FastAPI, PostgreSQL, Vue 3, Docker/Compose, Terraform, Ansible, WireGuard, GitLab CI/CD and Prometheus/Grafana baseline. PostgreSQL owns state and atomic claims. V1 introduces no Redis, message broker, Kubernetes or Slurm. Check compatible versions during first implementation.
- **Rationale:** Fewer services reduce operational surface while permitting transactional idempotency/claiming. This trades broker convenience for explicit SQL, leases, fencing and reconciliation responsibility.
- **Consequences:** Independent attempts, guarded terminal writes, durable validated artifacts, UNKNOWN reconciliation, strict environment/FIFO policy and permission checks are required. No exactly-once claim; automatic retries may wait.
- **Evidence:** [Lifecycle invariants](PLAN.md#data-and-lifecycle-invariants), [B2/B3 specifications](ai-evaluation/TASKS.md).
- **Decision basis:** User-specified technical/reliability baseline; no unverified version selection or fake lockfile.
- **Revisit trigger:** Measured scale/contention or operational needs justify added infrastructure after V1; change via a new ADR.
- **Date:** 2026-10-30 (UTC+08:00)
- **Implementation status:** Design only; no services/schema/queue implemented.
- **Validation evidence:** Runtime Not run; B2/B3 remain unprepared experiments, not production validation.

## ADR-004

- **Title:** Documentation responsibilities and evidence discipline
- **Status:** Accepted
- **Context:** Plans, dynamic progress and real execution evidence must not drift or imply delivery.
- **Options:** Duplicate roadmap/TODO documents; one overall plan with specialized linked records.
- **Decision:** Use one PLAN, a detailed phase checklist, STATUS for current facts, DECISIONS for choices, DEVELOPMENT_LOG for append-only work, ENVIRONMENT for checks, evaluation docs for protocols/results and runs for local raw evidence. Reserve empty implementation directories with .gitkeep only.
- **Rationale:** Single authoritative locations and explicit evidence labels make progress reviewable without repeated dynamic text.
- **Consequences:** Important work gets proportional checks/log entries; STATUS changes only with progress and ADRs only with material choices. Git requires explicit commit/push authorization; no global identity changes or bulk add. No fake run records.
- **Evidence:** [AGENTS](../AGENTS.md), [documentation map](../README.md#documentation-map), [runs policy](../runs/README.md).
- **Decision basis:** User-specified documentation/evidence requirements; routine engineering choices for relative links and empty-directory retention.
- **Revisit trigger:** Real duplication/drift, a phase needs its own detailed plan, or user changes reporting needs.
- **Date:** 2026-10-30 (UTC+08:00)
- **Implementation status:** Documents and skeleton created; ongoing discipline applies to future work.
- **Validation evidence:** Static initialization checks in [DEVELOPMENT_LOG](DEVELOPMENT_LOG.md).

## ADR-005

- **Title:** Trusted templates, synthetic data and secret boundaries
- **Status:** Accepted
- **Context:** Arbitrary user workloads and sensitive data would exceed this learning platform's scope and evidence controls.
- **Options:** Arbitrary uploaded code/images; fixed trusted CPU templates with registered public/synthetic data.
- **Decision:** V1 accepts only statistics and chart-rendering templates, explicit versions, registered datasets and allowlisted parameters/resources. Use synthetic/public data; keep credentials, Terraform state, real runtime volumes and raw sessions outside tracked artifacts. Object-level permissions apply to jobs/logs/results/cancellation.
- **Rationale:** Narrow workload trust makes reproducibility, isolation and failure semantics tractable without unsupported enterprise claims.
- **Consequences:** No arbitrary code/images or real private research data; at least two ordinary test users; inspect tracked files because ignore rules cannot retroactively protect them. Do not dump the environment or log secrets/personal absolute paths.
- **Evidence:** [Scope](PLAN.md#fixed-scope), [.gitignore](../.gitignore), [run boundaries](../runs/README.md).
- **Decision basis:** User-specified trust/data/security boundaries; ignore patterns are routine engineering choices, not a complete secret-detection system.
- **Revisit trigger:** New workload/data trust requirements; require explicit scope review and a new ADR before expansion.
- **Date:** 2026-10-30 (UTC+08:00)
- **Implementation status:** Documentation/ignore rules created; runtime enforcement unimplemented.
- **Validation evidence:** Representative ignore checks only; runtime permissions Not run.

## ADR-006

- **Title:** Public repository visibility with preserved Git history
- **Status:** Accepted
- **Context:** The repository was initially created private under the user's earlier instruction. The user subsequently requested public portfolio access, contingent on review of current files and full Git history, without replacing the repository or rewriting history.
- **Options:** Keep the repository private; publish the existing repository after review and acceptance of identified personal metadata; defer publication for remediation if other risks appear.
- **Decision:** Change the existing `Esports-Medusa/researchdock` repository to Public. Preserve its identity, files, two existing commits, main/upstream and settings. The user explicitly accepted publication of the personal email already present in author/committer metadata before the visibility change. Do not reproduce the email in documentation.
- **Rationale:** Current content is documentation/scaffolding intended for portfolio review. The only identified personal disclosure was historical commit email metadata, which was surfaced and accepted; no secret or raw-session content was identified by the review.
- **Consequences:** Historical author/committer email becomes publicly accessible and public copies may persist. Preserve history; changing future Git identity would not remove existing metadata. Continue reviewing future diffs/history and exclude secrets, private data and raw sessions. Public visibility does not enable deployment or change the CI/CD technical plan.
- **Evidence:** [Development log](DEVELOPMENT_LOG.md), [current status](STATUS.md), and https://github.com/Esports-Medusa/researchdock. GitHub API reports public visibility, the same repository ID, unchanged checked settings and Pages disabled.
- **Decision basis:** Explicit user instruction to publish after review, followed by explicit acceptance of the identified historical personal-email exposure. No inferred approval to expose other sensitive content or enable hosting.
- **Revisit trigger:** New secret/privacy findings, changed data/workload scope or a user request to change visibility. Stop relevant publication and seek instructions rather than rewriting history automatically.
- **Date:** 2026-10-30T23:02:43+08:00
- **Implementation status:** Existing repository visibility changed to Public; no new repository, history rewrite, Pages or cloud deployment.
- **Validation evidence:** Reviewed 22 current files, both reachable commits and all 33 local Git objects (20 blobs); remote references matched the reviewed main. Queried GitHub before and after the change and verified public visibility, unchanged identity and selected settings. Pattern/manual review is bounded evidence, not a guarantee against every possible secret.
