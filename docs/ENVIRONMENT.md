# Environment inspection

Inspection time: **2026-10-30T22:43:00+0800**, from `date '+%Y-%m-%dT%H:%M:%S%z'`. This is the local tool execution environment visible to this session. It is not evidence of a future Linux Worker, cloud host, VM, or all capabilities of the user's laptop. Paths in published records are repository-relative.

| Check command | Observed result | Classification |
| --- | --- | --- |
| `pwd`, `ls -la .` | Dedicated ResearchDock directory, initially empty | Executed |
| `git rev-parse --show-toplevel`, `git status --short --branch` | Initially not a Git repository or inside another repository | Executed |
| Explicit ancestor `AGENTS.md` existence checks, plus target listing | No applicable existing instructions found | Executed |
| `uname -srm` | Darwin 25.5.0 arm64 | Executed |
| `sw_vers` | macOS 26.5.2, build 25F84 | Executed |
| `sysctl -n hw.memsize` | Operation not permitted; memory amount Unknown | Executed; permission blocked |
| `df -h .` | Volume size 460 GiB; available 256 GiB at inspection | Executed; point-in-time capacity |
| `git --version` | Git 2.50.1 (Apple Git-155) | Executed |
| `python3 --version` | Python 3.14.7 | Executed; compatibility with future stack Unverified |
| `command -v docker`, `docker --version` | CLI not found in session PATH | Executed |
| `docker compose version` | Command unavailable because Docker CLI is absent | Executed; Compose version Unknown |
| `docker info --format '{{.ServerVersion}}'` | Command unavailable because Docker CLI is absent | Executed; daemon status Unknown |

A missing CLI differs from an installed CLI unable to connect to its daemon. No daemon connectivity conclusion is possible here. No software was installed. A denied optional memory query was not escalated; the unknown is recorded instead.

Git initialization was a separate authorized write action: the sandbox initially denied `.git` creation; an approved retry succeeded. Subsequent repository checks belong in [DEVELOPMENT_LOG](DEVELOPMENT_LOG.md).

## Unknown / Not checked

Physical memory; CPU core count; VM support and configured capacity; Linux execution node readiness; cloud account/permissions/region/spec/cost; actual AI model IDs, reasoning settings and account access; PostgreSQL, Terraform, Ansible, WireGuard and monitoring versions. These were outside this narrow inspection. No full environment-variable dump or unrelated project contents were read.

The macOS host does not satisfy the future Linux-node deployment acceptance by itself. Confirm dependency and image versions at implementation time; observed Python availability is not a pinned supported runtime. Recheck mutable capacity and versions when work begins.
