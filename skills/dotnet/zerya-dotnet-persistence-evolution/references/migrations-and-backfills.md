# Migrations and Backfills

## Plan the transition

Record:

```text
Old and target representations:
Authoritative writes during each phase:
Compatibility window and deployment order:
Backfill key and batch size:
Restart checkpoint and idempotency rule:
Structural and domain verification:
Anomaly handling and quarantine:
Forward-repair or rollback strategy:
Cleanup gate:
```

## Expand, migrate, contract

1. Add tables, columns, indexes, and nullable relationships without breaking old code.
2. Avoid expensive blocking operations in the same deployment unless their production impact is understood.
3. Make transitional reads and writes explicit. Use dual-write only for a bounded window, define failure semantics, and instrument mismatches.
4. Backfill in deterministic key order with bounded batches and durable checkpoints.
5. Record or quarantine malformed and ambiguous rows instead of guessing business meaning.
6. Verify counts, uniqueness, foreign keys, nullability, totals, state-machine rules, and sampled business scenarios.
7. Switch the authoritative writer before removing fallback reads.
8. Tighten constraints and remove legacy schema only after every supported application version no longer needs it.

Prefer forward repair when rollback would need to reverse already accepted business writes. Test migrations on production-like volume and provider versions. Estimate lock duration, log growth, index build behavior, and operational observability.
