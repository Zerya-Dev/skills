---
name: zerya-dotnet-modular-monolith
description: Design, implement, refactor, or review hard or soft module boundaries in an accepted .NET modular monolith. Use when a repository already uses or has explicitly chosen a modular monolith, when extracting a justified business capability, enforcing compiler boundaries, introducing module-owned DbContexts or schemas, defining Contracts, Queries and Integration Events, composing cross-module reads, orchestrating per-module migrations, or testing module dependencies. Do not use as the first response to an uncertain domain boundary, isolated EF migration problem, or Wolverine handler problem; diagnose those with the focused DDD, persistence, or Wolverine skill first.
---

# Zerya .NET Modular Monolith

Implement already-justified module boundaries. Before introducing or splitting a module, use `zerya-dotnet-ddd-architecture` to confirm the semantic boundary, `zerya-dotnet-persistence-evolution` for storage-only problems, and `zerya-wolverine-vertical-slices` for framework-only problems. Confirm that the proposed module is not merely an aggregate, entity, feature folder, integration adapter, persistence concern, or handler convention.

## Confirm the boundary

Proceed only when the repository already accepts modular monolith architecture or evidence shows a concrete need for module isolation. Record:

```text
Capability and business owner:
Reason a feature or separate aggregate is insufficient:
Write and transaction owner:
Data, DbContext, schema, and migration owner:
Public Queries and Integration Events:
Cross-module read strategy:
Authorization owner:
Project references and enforcement:
Migration and rollout sequence:
```

Choose the least rigid effective level:

- Use feature boundaries for namespaces and ownership within existing projects.
- Use soft modules for separate DbContexts, optional schemas, internal visibility, and architecture tests without projects per module.
- Use hard modules for compiler-enforced projects, Contracts, isolated persistence, and explicit communication.

## Enforce module ownership

- Give each business rule, write model, table, mapping, migration, and repair process one module owner.
- Keep Domain free of EF Core, ASP.NET, Wolverine, Infrastructure, and foreign modules.
- Let modules depend on another module only through producer-owned stable Contracts.
- Keep Contracts limited to immutable Integration Events, public Queries, and transport-neutral responses. Do not expose Commands, handlers, domain entities, DbContexts, or services.
- Never mutate another module's write model or use its DbContext. Avoid cross-module EF navigations and distributed local transactions.
- Publish completed facts as Integration Events when asynchronous reaction is appropriate. Use producer-owned Queries only for justified current semantics or authorization.
- Keep synchronous dependencies explicit, acyclic, and observable.
- Prefer compiler boundaries and access modifiers, then architecture tests, then review rules.

Read [architecture-model.md](references/architecture-model.md), [module-boundary-selection.md](references/module-boundary-selection.md), and [module-layout.md](references/module-layout.md) when defining structure or references.

## Route only to needed guidance

- For Host-facing APIs and Contracts, read [module-api.md](references/module-api.md).
- For Queries, Integration Events, outbox/inbox, retries, idempotency, and ordering, read [module-integration.md](references/module-integration.md).
- For module-owned DbContexts, schemas, migration histories, and write isolation, read [modular-persistence.md](references/modular-persistence.md).
- For cross-schema joins, SQL views, producer Queries, composition layers, and projections, read [cross-schema-read-models.md](references/cross-schema-read-models.md).
- For identity facts and resource authorization across boundaries, read [modular-authorization.md](references/modular-authorization.md).
- For deployment-time migration ordering and compatibility, read [migration-orchestration.md](references/migration-orchestration.md).
- For project rules and architecture tests, read [architecture-enforcement.md](references/architecture-enforcement.md).
- For review tasks, read [modular-review-checklist.md](references/modular-review-checklist.md).
- Read [sources.md](references/sources.md) only for provenance.

Do not load every reference by default. Do not introduce Integration Events, a schema, or a project per module without connecting it to the selected boundary and consistency model.
