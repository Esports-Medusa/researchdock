# Evaluation results

**Not run.** No seeds/evaluators have been prepared, self-checked or frozen. No measured scores, timings, usage, costs or winner exist. This is an empty evidence template, not an executed benchmark. See [protocol](PROTOCOL.md) and [tasks](TASKS.md).

## Candidate registry (confirm from real UI/CLI)

| Candidate | Role | Actual model_id/alias | Reasoning configuration | Tool/version | Availability evidence | Comparison type |
| --- | --- | --- | --- | --- | --- | --- |
| A | Current accessible coding configuration | Unknown | Unknown | Unknown | Unknown | Unknown |
| B | Optional second accessible configuration | Unknown | Unknown | Unknown | Unknown | Unknown |

Do not infer actual model identity from the initialization assistant. One accessible candidate means suitability only, `provisional, no comparative evidence`.

## Run index (empty)

| run_id | Candidate | Task | Status | First passed/total | Repair passed/total | First quality | Final quality | Sanitized evidence |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |

## Required per-run record fields

Copy this schema only when a real execution begins; raw records follow [runs format](../../runs/README.md).

```yaml
run_id: <unique actual execution ID>
candidate: <A or B>
task: <B1/B2/B3>
model_id_or_alias: Unknown
tool_version: Unknown
reasoning_configuration: Unknown
comparison_type: Unknown
availability_evidence: <sanitized UI/CLI source or Unknown>
seed_hash_manifest: <path and SHA-256>
prompt_sha256: <exact submitted prompt hash>
evaluator_and_rubric_hashes: <frozen manifest>
machine_and_dependencies: <observed versions, resource caps, permissions, cache policy>
time_limits: {first_minutes: 20, repair_minutes: 10, max_repairs: 1}
started_at: <actual ISO-8601 with UTC offset>
ended_at: <actual ISO-8601 with UTC offset>
setup_download_time: <observed separately or Unknown>
first_and_repair_processing_time: <observed separately>
status: <Completed/Incomplete/Timed out/Environment blocked/Tool failure/Invalid>
first_passed_total: <observed>
repair_passed_total: <observed or Not run>
critical_gates: <per-ID observed results>
human_corrective_prompt_count: <observed>
human_manual_edits_and_active_time: <observed>
quality_scores: <first/final per dimension and totals, or Unscored with reason>
visible_tokens: Unknown
visible_cost: Unknown
output_paths: <local relative paths for first/repair snapshots and raw results>
limitations: <isolation, noise, preparer involvement and low sample count>
```

## Aggregate and decision (empty)

| Candidate | Valid tasks/repeats | First mean | Final mean | All critical gates | Human intervention | Visible cost | Suitability / decision |
| --- | --- | --- | --- | --- | --- | --- | --- |

Blocked tasks do not establish failure or a complete mean. Retain first results, failed runs and repair evidence. Link the decision to [ADR-001](../DECISIONS.md#adr-001) only after evidence exists.
