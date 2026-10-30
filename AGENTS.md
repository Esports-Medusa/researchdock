# ResearchDock collaboration rules

Read this file, [STATUS](docs/STATUS.md), the relevant [plan](docs/PLAN.md) / [phase](docs/phases/PHASE_01.md), and [decisions](docs/DECISIONS.md) before work. Use Chinese with the user; use clear English for repository Markdown, identifiers, comments and docstrings. External reference quotations may retain their source language.

## Scope and evidence

- Preserve existing work and history. Inspect cwd, repository root, applicable instructions and Git status before editing. Do not scan personal directories or unrelated projects.
- Follow the fixed scope in PLAN. Do not expand cloud spending, subscriptions, public exposure, deployment, or implementation authorization by inference.
- Do not start sub-agents unless explicitly requested by the user.
- A plan is not implementation; a script's existence is not execution; passing tests does not establish all reliability claims.
- Distinguish **Observed / Planned / Unverified** and **Executed / Static-only / Not run**. Record environment blockers separately from implementation failures.
- Verify important changes proportionately and append actual work to DEVELOPMENT_LOG. Update STATUS only when progress changes, and DECISIONS only for material tradeoffs. A formatting edit does not require updating every record.
- Each fact has one authoritative location: PLAN for scope, Phase 01 for tasks, STATUS for progress, ENVIRONMENT for environment, ADRs for choices, RESULTS for evaluation, DEVELOPMENT_LOG for chronology. Link instead of copying dynamic facts.
- Create run records only for real executions; preserve first failures and repair evidence. Correct previous statements by appending an explanation rather than erasing evidence. Do not invent scores, measurements, releases or deployment claims.
- Use only synthetic or public data. Do not record secrets, environment-variable values, personal absolute paths, or full sensitive configurations in logs. Never dump the entire environment to inspect configuration.

## Git and external actions

Do not automatically commit or push, change global Git identity, bulk `git add .`, rewrite history, or create remote repositories. Obtain explicit authorization for the corresponding Git action. Git initialization in the dedicated empty directory was authorized for initialization; it does not authorize later commits.

Ignore rules do not protect tracked files: review `git ls-files` as well as ignore behavior before adding artifacts. Raw sessions, real logs and results stay local under ignored paths; only reviewed, sanitized summaries belong in docs. Never commit credentials or real Terraform state/variables.

## Reliability constraints

PostgreSQL is the task-state authority. Preserve attempt fencing, atomic claims, leases, heartbeats, output isolation, explicit UNKNOWN reconciliation and confirmed container termination for cancellation. No exactly-once claim. Do not convert local jobs to cloud jobs automatically. Experimental B2/B3 code is not the formal M2/M3 platform.

Versions must be checked for compatibility at first implementation and recorded with evidence; do not invent lockfiles or verified launch commands. See [run evidence format](runs/README.md) and [evaluation controls](docs/ai-evaluation/PROTOCOL.md).
