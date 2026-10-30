# ResearchDock

ResearchDock is a planned local/public-cloud task platform for small research and R&D teams. It develops and demonstrates junior DevOps, developer productivity, cloud operations, and research computing support skills. FigColor remains the separate full-stack/AI application portfolio project.

**Actual maturity:** documentation and directory scaffolding only. No API, Worker, UI, task image, deployment, benchmark implementation, or model comparison exists. M1 and Phase 01 are incomplete. See [current facts and next actions](docs/STATUS.md).

Researchers will submit trusted CPU task templates, select an environment and resources, follow status/logs, cancel work, and download results. Administrators will manage nodes, releases, incidents, and recovery. This is not a replica of university BEAR, enterprise HPC, or a complete private cloud; a local VM is a local execution node.

## Documentation map

| File | Authority |
| --- | --- |
| [PLAN](docs/PLAN.md) | Single overall plan, scope, milestones and learning route |
| [Phase 01](docs/phases/PHASE_01.md) | Actionable M0 + AI evaluation + M1 checklist |
| [STATUS](docs/STATUS.md) | Current facts, blockers and ordered next actions |
| [DECISIONS](docs/DECISIONS.md) | Stable ADRs and changes in reasoning |
| [DEVELOPMENT_LOG](docs/DEVELOPMENT_LOG.md) | Timestamped work actually performed |
| [ENVIRONMENT](docs/ENVIRONMENT.md) | Observed environment and unknowns |
| [Evaluation protocol](docs/ai-evaluation/PROTOCOL.md) | Fairness, scoring and selection rules |
| [Evaluation tasks](docs/ai-evaluation/TASKS.md) | Frozen-task specifications and standalone prompts |
| [Evaluation results](docs/ai-evaluation/RESULTS.md) | Evidence-backed results; currently Not run |
| [Benchmark preparation](benchmarks/README.md) | Future seed, fixture and evaluator freeze rules |
| [Run records](runs/README.md) | Actual execution evidence and raw-log boundaries |
| [AGENTS](AGENTS.md) | Stable engineering collaboration rules |

Reserved directories: `api/`, `worker/`, `web/`, `infra/`, `tests/`, `scripts/`, `examples/`. Their `.gitkeep` files only preserve empty directories.

## Next action

Confirm up to two actually available tool/model configurations, then prepare, self-check and freeze neutral benchmark packages according to Phase 01. No application quick-start command has been verified. Planned task/container commands must be documented and executed during M1 before being presented as reproducible instructions.
