# Actual execution records

Create `runs/<run_id>/` only for a real command/session execution. This initialization records its static checks in [DEVELOPMENT_LOG](../docs/DEVELOPMENT_LOG.md), without creating a model-run record. Everything under this directory except this README is ignored by default; review tracked files too because ignore rules cannot untrack existing files.

Each real run record must include:

- Stable run ID, purpose/task/candidate, actual start/end timestamps and UTC offset; tool/model versions and reasoning setting from observed sources or Unknown.
- Repository/package revision or frozen hash manifest, exact prompt hash, synthetic fixture hash, dependency and machine versions, CPU/memory limits, network/tool permissions and cache/setup policy.
- Actual commands (redacted and repository-relative), working directory relative to the sandbox, exit codes, stdout/stderr paths, evaluator version/hash and fixed passed/total assertions.
- First output/code snapshot and hashes preserved before repair; separate repair round ID, provided failure output, prompts/manual edits and repaired snapshot/results. Never overwrite the original failure.
- Setup/download time separate from processing/human active time; observed token/cost statistics or Unknown.
- Output/artifact paths and checksums, failure cause, environment vs implementation classification, unresolved limits and any later correction appended with a timestamp.

Suggested future local layout: `record.md`, `commands.txt`, `first/`, `repair-1/`, `raw/`, `artifacts/`, `checksums.sha256`. It is a format, not an instruction to manufacture empty evidence now. Evaluation metadata also follows [RESULTS schema](../docs/ai-evaluation/RESULTS.md).

Never store real secrets, environment-variable values, personal absolute paths, full sensitive configuration or private datasets in these records. Raw sessions may contain sensitive material and stay local; inspect/redact before promoting small summaries into docs. Track sanitized scoring summaries in `docs/ai-evaluation/RESULTS.md`, not raw logs. Append corrections and preserve failed/invalid/blocked evidence.
