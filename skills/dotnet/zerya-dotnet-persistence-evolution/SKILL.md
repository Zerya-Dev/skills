---
name: zerya-dotnet-persistence-evolution
description: Design, implement, refactor, or review .NET persistence and data evolution with EF Core. Use when mapping DDD aggregates, separating write and read models, changing DbContexts or schemas, handling optimistic concurrency and transactions, planning expand-contract migrations, running backfills, removing legacy relationships, or diagnosing persistence coupling without redefining domain boundaries.
---

# Zerya .NET Persistence Evolution

Implement an explicit domain and ownership decision without letting an EF graph, migration convenience, or query shape redefine it. If aggregate or Bounded Context ownership is unclear, use `zerya-dotnet-ddd-architecture` first.

## Establish the persistence contract

1. Read repository instructions, supported database providers, deployment topology, and accepted ownership decisions.
2. Trace affected writes, reads, mappings, constraints, migrations, jobs, imports, and repair paths.
3. Record the authoritative representation, transaction boundary, concurrency token, retention rules, and rollout constraints.
4. Separate domain-model changes from storage changes and query optimizations.
5. Define verification and forward-repair before moving material data.

Read [aggregate-persistence.md](references/aggregate-persistence.md) for EF Core mappings and [transactions-and-concurrency.md](references/transactions-and-concurrency.md) when correctness depends on atomicity, retries, or conflicting writes.

## Evolve data safely

Prefer an observable expand-migrate-contract sequence:

1. Expand the schema compatibly.
2. Deploy code that tolerates the transition state when staged rollout requires it.
3. Backfill deterministically in restartable batches.
4. Verify structural and domain-specific invariants.
5. Switch authoritative writes, then reads.
6. Tighten constraints after verification.
7. Remove obsolete compatibility code and schema in a later change.

Read [migrations-and-backfills.md](references/migrations-and-backfills.md) before changing live data. Do not combine the first data move with destructive cleanup.

## Keep reads independent from writes

Project query shapes without inflating aggregate boundaries. Use a direct local query while it remains clear, secure, and operationally acceptable. Add a persisted projection only for demonstrated latency, scale, availability, search, analytics, or storage needs.

Read [read-models.md](references/read-models.md) for query and projection decisions.

## Route adjacent concerns

- Use `zerya-dotnet-ddd-architecture` when persistence evidence reveals an uncertain aggregate, invariant, ownership, or Bounded Context.
- Use `zerya-wolverine-vertical-slices` when Wolverine owns transaction middleware, outbox, inbox, retries, or handler persistence.
- Use `zerya-dotnet-modular-monolith` only for already-justified module-owned schemas, DbContexts, or migration orchestration.

For reviews, read [persistence-review.md](references/persistence-review.md). Report evidence, failure mode, rollout risk, smallest safe correction, and verification. Read [sources.md](references/sources.md) only for provenance or exact framework behavior.
