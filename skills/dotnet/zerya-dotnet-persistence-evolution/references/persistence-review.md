# Persistence Review

1. Trace representative write and read paths to generated SQL and schema ownership.
2. Compare EF mappings with declared aggregate and lifecycle boundaries.
3. Identify cross-aggregate cascades, accidental navigations, oversized loads, missing constraints, tracking misuse, and broad concurrency tokens.
4. Review transaction, retry, outbox, and failure behavior together.
5. Review every migration for mixed application versions, locks, data anomalies, restartability, verification, and cleanup timing.
6. Review projections for freshness, idempotency, repair, authorization, and deletion.

Classify findings as mapping mismatch, persistence coupling, transaction mismatch, concurrency defect, unsafe migration, unverifiable backfill, query inefficiency, read/write confusion, or projection-operability gap.

For each material finding provide file and line evidence, affected data, realistic failure mode, smallest safe correction, rollout order, and verification.
