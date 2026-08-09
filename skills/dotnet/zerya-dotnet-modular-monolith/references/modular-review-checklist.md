# Modular Review Checklist

## Contents

- [Ownership and project references](#ownership)
- [Application API, handlers, and domain](#application-api)
- [Events and synchronous queries](#integration-events)
- [Local and cross-module reads](#local-reads)
- [Authorization and ordering](#authorization)
- [Verification](#verification)

## Ownership

- What business capability owns this behavior?
- Is a rule in Host or SharedKernel because ownership was unclear?
- Does the write touch exactly one module-owned persistence boundary?
- Is a foreign concept translated into local language?

## Project References

- Does Host reference only Application, Infrastructure, ReadModels, and SharedKernel?
- Does a module reference only foreign Contracts, and only for Queries invoked or Events consumed?
- Is the cross-module Query graph acyclic?
- Has any `Application -> Application` dependency appeared?
- Does ReadModels reference a module's Application, Domain, or Infrastructure?
- Does cross-module composition live in Host without a temporary migration ADR, owner, and removal plan?
- Does SharedKernel contain a concrete module contract?

## Application API

- Is the new Command or Query intended for Host?
- Can the contract expose less data?
- Does any code call a handler directly?
- Is a Command or Query being used by another module?

## Handler

- Is the happy path visible?
- Does it load local state, invoke named behavior, and return?
- Does it contain domain-status switches, workflow loops, or fallback logic?
- Does it call `SaveChangesAsync()` unnecessarily?
- If it changes state, does it use only the owning module's write DbContext and transaction boundary?
- If it injects a DbContext abstraction, does Wolverine map it to the concrete context and does DI resolve both to the same scoped instance?
- Does it enlist another module's DbContext or write to another module's schema?
- Is Wolverine discovering the handler and validator?

## Domain

- Are invariants protected by the aggregate?
- Is the aggregate boundary based on lifecycle and consistency?
- Are foreign concepts referenced by IDs or translated facts?
- Is a domain service free of EF, Wolverine, and external clients?
- Does any domain event leave the module?

## Integration Events

- Is the event a completed fact?
- Is it published by the state owner?
- Is `Publish` used rather than `Invoke`?
- Does the publisher avoid assumptions about consumers?
- Does the contract avoid domain and infrastructure types?
- Is the consumer idempotent?
- Does the consumer mutate only local state?
- Is a local Command genuinely useful or merely ceremony?

## Synchronous Cross-Module Queries

- Does the consumer need a current answer, producer-owned semantics, or producer-owned authorization?
- Is the Query intentionally exposed under the producer's `Contracts.Queries`?
- Is the response narrow and transport-neutral?
- Does the handler read only producer-owned data?
- Does the dependency avoid cycles, fan-out, and nested Query chains?
- Is a Query invoked rather than published?
- Has any foreign Command, Command in Contracts, or Application-to-Application reference been introduced?

## Local Reads

- Does the query use only module-owned data?
- Are filtering, ordering, and paging performed in SQL?
- Is a separate projection table actually necessary?
- Is the response feature-local?

## Cross-Module Read Models

- Is the query in a dedicated application-level composition layer rather than inside one business module?
- Does the read use the default direct read-only cross-schema read model when no concrete requirement justifies an exception?
- When it departs from the default, does the decision address freshness and consistency, schema stability, sensitivity and authorization, change frequency, projection cost, or expected module separation?
- Does one `ApplicationReadDbContext` map every table participating in the LINQ query?
- Are mappings minimal read rows rather than module domain entities?
- Is the query no-tracking and projected in SQL with filtering, ordering, grouping, and paging before materialization?
- Does the read context avoid writes and migrations for module-owned source tables?
- If a persisted projection exists, does a separate projection-owned write context map and migrate only projection-owned tables?
- Are cross-schema dependencies and provider-sensitive behavior covered by SQL Server integration tests?
- Are directly read tables and columns acknowledged as persistence dependencies?
- Is a producer-owned SQL view defined and migrated by the producer?
- Does a sensitive detail read delegate current resource authorization to the owning module?
- If an event-fed projection is proposed, is it justified by performance, denormalization, independent storage, availability, scaling, analytics, search, or external integration?
- Why are a direct cross-schema read, producer-owned SQL view, and producer-owned Query insufficient?
- Has the projection's backfill and repair path been sketched, and is its complexity proportionate to the demonstrated benefit?
- If data is persisted, are eventual consistency, idempotency, failure handling, migrations, and backfills addressed?

## Authorization

- Does Identity provide identity facts only?
- Does the resource-owning module decide access?
- Does the code work outside HTTP?
- Is sensitive authorization based on current owner state rather than stale projection data?

## Ordering

- Is ordering infrastructure being introduced without a demonstrated need?
- If correctness depends on order, is the specific flow documented and tested?
- Could the consumer instead be idempotent or order-independent?

## Verification

- Domain tests updated?
- SQL Server integration tests updated?
- Wolverine pipeline tests updated?
- Cross-schema query tests updated, plus projection sequence tests when an event-fed projection exists?
- Architecture tests updated?
- Changed files, commands, and remaining risks reported?
