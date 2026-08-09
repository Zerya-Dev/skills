---
name: zerya-dotnet-ddd-architecture
description: Discover, design, refactor, or review .NET domain models with strategic and tactical DDD. Use when clarifying ubiquitous language, subdomains, Bounded Contexts, context relationships, aggregate and entity boundaries, invariants, domain services, application processes, domain events, or misplaced business rules. Use it to diagnose domain-model problems before choosing persistence or modular-monolith boundaries; use a persistence- or framework-specific skill only after the domain decision is explicit.
---

# Zerya .NET DDD Architecture

Treat architecture as a set of evidence-backed model decisions, not a ladder from classes to projects. Keep semantic, consistency, ownership, and implementation boundaries distinct.

## Establish domain evidence

1. Read repository instructions and accepted architecture decisions.
2. Identify the business problem, actors, outcomes, policies, terminology, and disputed meanings.
3. Consult domain experts or existing domain artifacts when available. Mark boundaries inferred only from code as hypotheses.
4. Map the relevant subdomains, candidate Bounded Contexts, upstream/downstream relationships, and language translations.
5. Trace representative commands, queries, events, external imports, and failure cases through the current implementation.
6. Record true business invariants, acceptable inconsistency windows, ownership, and independent lifecycles.

Read [strategic-modeling.md](references/strategic-modeling.md) when discovering or changing Bounded Contexts. Do not infer a context solely from folders, tables, teams, or deployment units.

## Make separate decisions

Decide each axis on its own evidence:

- **Model meaning:** define where one ubiquitous language and internally consistent model apply.
- **Consistency:** cluster only state that must satisfy named invariants atomically.
- **Behavior:** place decisions with the domain concept that has the information and responsibility to make them.
- **Ownership:** assign authoritative writes and lifecycle responsibility explicitly.
- **Implementation:** choose namespaces, projects, DbContexts, schemas, or processes only to enforce already justified boundaries.

For aggregate design, read [aggregate-boundaries.md](references/aggregate-boundaries.md). For behavior and rule placement, read [ddd-and-rule-placement.md](references/ddd-and-rule-placement.md). For coordination across aggregates, read [application-processes.md](references/application-processes.md).

## Preserve domain integrity

- Keep invariants and state transitions in the domain model.
- Reference other aggregates by identity unless a repository-specific mechanism preserves the same boundary without exposing mutable cross-aggregate graphs.
- Modify one aggregate instance per transaction as a strong default, not an absolute law. Name the invariant before making a multi-aggregate write atomic.
- Let Application load authoritative facts, authorize the use case, invoke domain decisions, and coordinate I/O.
- Use domain events for meaningful completed domain facts. Choose synchronous or asynchronous handling from consistency and delivery requirements, not from event naming alone.
- Keep simple CRUD simple when no valuable domain model is present.

Read [authorization.md](references/authorization.md) only when permissions depend on domain state or resource ownership.

## Route implementation work

- Use `zerya-dotnet-persistence-evolution` after changing aggregate persistence, EF Core mappings, transactions, read models, schema, or data.
- Use `zerya-wolverine-vertical-slices` for Wolverine handlers, middleware, outbox, scheduling, or routing.
- Use `zerya-dotnet-modular-monolith` only after a business boundary and concrete isolation benefit are established.

For reviews, read [architecture-review.md](references/architecture-review.md), report findings by severity with evidence and consequences, and distinguish facts from modeling hypotheses. Do not edit during a review unless requested. Read [sources.md](references/sources.md) only for provenance or further study.
