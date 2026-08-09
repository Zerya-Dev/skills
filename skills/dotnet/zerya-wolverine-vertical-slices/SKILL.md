---
name: zerya-wolverine-vertical-slices
description: Design, implement, refactor, or review .NET vertical slices built with Wolverine. Use for message and HTTP handlers, command and query pipelines, validation, EF Core transactional middleware, DbContext abstractions, cascading messages, transactional outbox or inbox, durable messaging, retries, scheduling, handler discovery, generated chains, or Wolverine-specific testing and diagnostics.
---

# Zerya Wolverine Vertical Slices

Use Wolverine capabilities deliberately and verify behavior against the installed version and generated handler chains. Do not turn local project conventions into universal framework rules.

## Inspect before changing

1. Read repository instructions and existing slice conventions.
2. Identify the Wolverine, persistence-provider, and integration package versions.
3. Inspect bootstrap configuration, discovery policies, middleware, durability, routing, and storage registrations.
4. Trace the complete handler chain, transaction owner, outgoing messages, retries, and failure path.
5. Preserve accepted result, validation, and endpoint conventions unless the task changes them.

Read [handlers-and-slices.md](references/handlers-and-slices.md) for slice design. Read [efcore-transactions-and-outbox.md](references/efcore-transactions-and-outbox.md) whenever a handler writes through EF Core or emits durable messages.

## Build the slice

- Give each message one clear intent and owner.
- Keep message-shape validation separate from domain invariants.
- Load only the state needed by the use case.
- Invoke domain behavior or an application process instead of encoding business meaning in pipeline plumbing.
- Make transaction, durability, idempotency, retry, and ordering requirements explicit.
- Return or publish messages according to Wolverine semantics verified for the installed version.
- Keep queries non-transactional unless they intentionally participate in a consistency operation.

Read [messaging-and-integration.md](references/messaging-and-integration.md) for cascading messages, invoke versus publish, durable delivery, and consumers. Read [scheduling-and-diagnostics.md](references/scheduling-and-diagnostics.md) for delayed work, generated code, discovery, and routing diagnostics.

## Respect adjacent boundaries

- Use `zerya-dotnet-ddd-architecture` when a handler contains unresolved business rules, aggregate decisions, or domain events.
- Use `zerya-dotnet-persistence-evolution` for EF mappings, concurrency design, read models, schema changes, or backfills.
- Use `zerya-dotnet-modular-monolith` for ownership and contracts between accepted modules; Wolverine only implements that decision.

For reviews, read [wolverine-review.md](references/wolverine-review.md). Report the observed configuration, generated behavior, consequence, correction, and verification command. Read [sources.md](references/sources.md) when exact APIs or version behavior matter.
