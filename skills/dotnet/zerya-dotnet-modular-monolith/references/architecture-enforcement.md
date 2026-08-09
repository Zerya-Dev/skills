# Architecture Enforcement

## Contents

- [Enforcement order](#enforcement-order)
- [Project, namespace, and public API tests](#project-reference-tests)
- [Integration and data ownership](#integration-boundary-tests)
- [Read models and Wolverine](#readmodels-tests)
- [Test matrix and ADR triggers](#test-matrix)

## Enforcement Order

Prefer:

1. compiler and project references;
2. access modifiers;
3. architecture tests;
4. static analysis;
5. code review and ADRs.

Do not rely on documentation alone.

## Project Reference Tests

Build a graph from `.csproj` references and assert:

- Domain references only SharedKernel and permitted framework-neutral packages;
- Contracts references only SharedKernel and framework-neutral contract packages;
- Application references own Domain, own Contracts, SharedKernel, and foreign Contracts only for Queries invoked or Events consumed;
- Infrastructure references only own Application, own Domain, and SharedKernel;
- ReadModels references SharedKernel and foreign Contracts only for producer Queries or event-fed projections it consumes;
- Host references Application, Infrastructure, ReadModels, and SharedKernel;
- no Application-to-Application references;
- no module references foreign Domain or Infrastructure;
- the directed graph of cross-module Query dependencies is acyclic.

## Namespace and Assembly Tests

Assert:

- `MOTOQ.Module.X.*` namespaces live in module X projects;
- Domain does not reference EF Core, ASP.NET, Wolverine, HTTP, Contracts, or Infrastructure;
- Contracts contains only Query requests, Query responses, Integration Events, and contract-specific primitives, with no handlers or framework dependencies;
- Infrastructure types do not leak into Application responses;
- SharedKernel contains no module vocabulary.

## Public API Tests

Allow public Application request and response types intended for Host.

Flag foreign references to:

```text
*Handler
*Validator
*Policy
*Service
*DbContext
*Configuration
```

Handler classes may be public for Wolverine, but Host and modules must never call them directly.

## Integration Boundary Tests

Assert:

- modules reference only foreign `*.Contracts`;
- no Contracts project contains Commands, handlers, validators, services, repositories, domain types, EF types, or Wolverine APIs;
- foreign `InvokeAsync` targets only `Contracts.Queries`;
- no module invokes a foreign Command, including through an allowlist or ADR exception;
- no Query is published and no Integration Event is invoked;
- foreign Query dependencies are acyclic and do not form nested fan-out chains;
- no domain event type is referenced outside its module;
- Integration Event handlers live in the consumer's Application or in ReadModels when implementing a justified event-fed projection.

Avoid brittle source-text tests when project-reference or assembly analysis can enforce the rule more accurately.

## Data Ownership Tests

Check for:

- foreign module DbContext types;
- foreign schema names in business-module runtime SQL;
- EF navigation to foreign domain types;
- migrations altering another module's tables;
- a global write DbContext;
- writes from ReadModels to module-owned tables;
- SharedKernel persistence abstractions.

Allow foreign schema names only in dedicated ReadModels, coordinated migrations or administrative scripts with explicit ownership. Treat direct table and column use as an accepted persistence dependency.

## ReadModels Tests

Assert:

- ReadModels references no module Application, Domain, or Infrastructure;
- cross-schema read rows are minimal persistence representations, not imported domain entities;
- one LINQ query does not combine `IQueryable` instances from different DbContexts;
- `ApplicationReadDbContext` does not own migrations for module source tables and cannot persist changes to them;
- persisted projections use a separate projection-owned write context that maps and migrates only projection-owned tables;
- cross-schema queries, views, migrations, and administrative scripts do not mutate module-owned source tables;
- producer-owned views are migrated by their producer and expose only intentional fields;
- sensitive reads use producer-owned authorization or demonstrate an equivalent current-state policy;
- event handlers are idempotent when a justified event-fed projection is used;
- projection backfills write only projection-owned tables.

## Wolverine Verification

When discovery or middleware changes, verify:

- every intended message has one correct handler or the intended separated consumers;
- handler assemblies are included;
- transaction middleware targets the correct module context;
- every injected DbContext abstraction is explicitly mapped to its concrete context in Wolverine and resolves to the same scoped instance;
- Queries are non-transactional;
- validators are discovered;
- outgoing Integration Events use durable behavior where required;
- cross-module Queries use `InvokeAsync`, while Integration Events use `PublishAsync` or the repository-equivalent Wolverine APIs.

Use repository-supported Wolverine diagnostics.

## Test Matrix

### Domain unit tests

Use for invariants, value objects, transitions, policies, and domain-event production.

### Application tests

Use for use-case orchestration and authorization behavior.

### EF SQL Server integration tests

Use for projections, tracking, concurrency, JSON, migrations, and graph persistence.

### Wolverine pipeline tests

Use for validation, transactions, rollback, outbox, inbox, retry, routing, and event consumers.

### Cross-module read-model tests

Use SQL Server integration tests for joins, null handling, filtering, paging, generated SQL, schema mappings, and migration exclusions. For event-fed projections, also test completeness, idempotency, ordering assumptions, and eventual consistency.

### Host/API tests

Use for HTTP mapping, authentication plumbing, OpenAPI, and delegation.

## ADR Triggers

Create or update an ADR when:

- adding or splitting a module;
- transferring data ownership;
- introducing a new cross-module Integration Event dependency;
- introducing a new synchronous cross-module Query dependency;
- adding a new persisted event-fed cross-module projection;
- moving a cross-module read to independent physical storage;
- allowing a temporary Host composition;
- introducing a repository exception;
- sharing a new business primitive;
- changing transaction or delivery semantics;
- adding special event ordering behavior.
