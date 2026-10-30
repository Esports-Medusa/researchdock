# Benchmark preparation and freeze rules

This directory currently contains a specification only. **There is no runnable prepared benchmark.** [TASKS](../docs/ai-evaluation/TASKS.md) and [PROTOCOL](../docs/ai-evaluation/PROTOCOL.md) are authoritative for contracts and fairness.

Future preparation layout (not yet created): reviewed public `seeds/B1`, `seeds/B2`, `seeds/B3`, `fixtures/`, `contracts/`, and a freeze manifest here. Private references, expected oracle results where required, fixed acceptance programs and self-check logs belong under ignored `.local/ai-eval-preparation/`, outside candidate access. A manifest may track their relative IDs and hashes without exposing answers. Candidate B1 receives the specified example expected JSON; hidden-case answers and B2/B3 reference implementations stay private.

Preparation procedure:

1. Build neutral minimal seeds, deterministic synthetic CSV and PostgreSQL fixtures, exact response/error contracts and permitted file lists. Record actual versions, engine, UID/mount expectations, resources and schema-reset procedure. Do not reuse a candidate's changed code as another seed.
2. Prepare fixed independent acceptance programs and passing reference implementations. Verify every positive and negative case. Run B3's defective version and prove the synchronized reproducer fails on the double-claim; verify the reference fix succeeds and error transactions release locks.
3. Map each critical gate and review checklist in the protocol to frozen assertion IDs. Record actual self-check commands, exit codes and artifacts; a written test description is not a passing test.
4. Review for biased hints/reference leaks, optionally with the user. Disclose involvement of the initialization/preparation assistant.
5. Freeze canonical seeds, fixtures, exact task prompts including substitutions, evaluator, expected contracts, scorecard and dependency manifest using per-file SHA-256 hashes. Record the complete file set and freeze time; keep a read-only master copy. No auto-commit is necessary.
6. Copy masters into isolated per-candidate/task/run sandboxes; verify hashes before each attempt. Never expose other results/reference code. Do not treat directory separation as an enforced access boundary.
7. A changed frozen file requires a new package version and symmetric reruns of affected tasks; retain superseded evidence.

Track only reviewed small synthetic fixtures, definitions and sanitized summaries. No real research dataset, credentials, raw database data, image layers or raw sessions. Do not claim readiness until reference self-check and freeze evidence exist. Runtime prerequisites unavailable means Blocked/Not run, especially Docker and real PostgreSQL concurrency tests.
