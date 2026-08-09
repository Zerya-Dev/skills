# Architecture Model

## Contents

- [Deployment and project set](#byoi-deployment)
- [Dependency graph](#dependency-graph)
- [Host and read-model responsibilities](#host-responsibilities)
- [Feature-first layout](#feature-first-layout)
- [SharedKernel](#sharedkernel)

## BYOI Deployment

The system has one deployment unit, one process boundary, and one physical SQL Server database:

```text
MOTOQ.Host
  ├── Identity module
  ├── DocumentCases module
  ├── Payments module
  ├── other business modules
  └── MOTOQ.ReadModels
```

A module is a business capability, not a technical layer or CRUD group.

Each module owns a separate SQL schema and write model. Deploy the application and run all module migrations together. Sharing the physical database permits direct read composition but does not transfer write ownership.

## Module Project Set

```text
MOTOQ.Module.<Name>.Application
MOTOQ.Module.<Name>.Domain
MOTOQ.Module.<Name>.Infrastructure
MOTOQ.Module.<Name>.Contracts
```

### Application

Owns:

- public Commands and Queries invoked by Host;
- response DTOs;
- Wolverine handlers;
- validation;
- local authorization orchestration;
- application services and processes;
- module-local read queries;
- ports implemented by Infrastructure;
- Integration Event consumers;
- translation of domain facts into public Integration Events.

### Domain

Owns:

- aggregates and entities;
- value objects;
- invariants and state transitions;
- domain policies and services;
- domain errors;
- domain events.

### Infrastructure

Owns:

- module DbContext;
- EF mappings and migrations;
- external clients and adapters;
- Wolverine and DI registration;
- infrastructure workers;
- technical configuration.

### Contracts

Owns the stable public messages produced or answered by the module.

It contains:

- synchronous Query contracts and their transport-neutral responses;
- immutable Integration Event contracts under `Events`;
- stable contract-specific primitives where necessary.

It does not contain:

- Commands;
- handlers;
- validators;
- domain entities;
- DbContexts;
- Wolverine APIs;
- application ports;
- service interfaces.

## Dependency Graph

```text
SharedKernel
  -> no module projects

Module.X.Domain
  -> SharedKernel

Module.X.Contracts
  -> SharedKernel when needed

Module.X.Application
  -> Module.X.Domain
  -> Module.X.Contracts
  -> Module.Y.Contracts only for Queries invoked or Events consumed
  -> SharedKernel

Module.X.Infrastructure
  -> Module.X.Application
  -> Module.X.Domain
  -> SharedKernel

ReadModels
  -> SharedKernel
  -> Module.*.Contracts for producer Queries or justified event-fed projections

Host
  -> Module.*.Application
  -> Module.*.Infrastructure
  -> ReadModels
  -> SharedKernel
```

No Application-to-Application dependency is allowed.

Cross-module synchronous dependencies must be Query-only and acyclic. Integration Events remain asynchronous facts. Cross-module Commands are forbidden without exception in this architecture. An ADR may change the repository's target architecture, but it does not create a local exception to this skill; surface that conflict before implementation.

## Host Responsibilities

Host owns:

- process startup and shutdown;
- dependency injection composition;
- Wolverine global setup and assembly registration;
- ASP.NET routing and HTTP mapping;
- authentication middleware;
- coarse endpoint policy plumbing;
- OpenAPI;
- health checks and observability composition;
- migration orchestration;
- installation-level configuration;
- delegation to module Commands, Queries, and ReadModels.

Host does not own:

- aggregate mutations;
- business workflows;
- resource-specific business authorization;
- module persistence mappings;
- cross-module data repair logic;
- business fallback rules;
- long-lived cross-module projection state.

## ReadModels Responsibilities

`MOTOQ.ReadModels` or `Application.ReadModels` composes read-only data from multiple module schemas. It normally owns query handlers, response models, minimal EF read rows, and a read-only `ApplicationReadDbContext`; source modules retain ownership of the mapped tables.

Default to a direct read-only cross-schema read model in this composition layer. Use producer-owned SQL views when producers must stabilize exposed database shapes and producer-owned Wolverine Queries when current producer semantics or authorization is required. Treat event-fed projections as a last resort for demonstrated quality-attribute or integration requirements that simpler cross-schema reads, views, or producer Queries cannot satisfy. Account for backfill and repair complexity before selecting one. Record the reason and trade-off when departing from the default.

It is not:

- a bounded context;
- a domain module;
- a write-side source of truth;
- an owner of mapped source tables;
- a way to mutate module data or bypass module invariants.

When a justified event-fed projection persists state, keep its write model separate from `ApplicationReadDbContext`. Use a projection-owned `ProjectionWriteDbContext` that maps and migrates only projection-owned tables. Query code may map those tables read-only in `ApplicationReadDbContext`, but no projection write may weaken the read context's `SaveChanges` guard.

## Feature-First Layout

```text
MOTOQ.Module.Identity.Application/
  Roles/
    CreateRole.cs
    DeleteRole.cs
    GetUserRoles.cs
  Users/
  Groups/

MOTOQ.Module.Identity.Domain/
  Roles/
    Role.cs
    RoleId.cs
    RoleErrors.cs
  Users/
  Groups/

MOTOQ.Module.Identity.Infrastructure/
  Roles/
    RoleConfiguration.cs
  Users/
  Groups/
  Persistence/
    IdentityDbContext.cs
    Migrations/
  ModuleRegistration.cs

MOTOQ.Module.Identity.Contracts/
  Queries/
    GetUserDisplayName.cs
  Events/
    UserDisabledIntegrationEvent.cs
    UserDisplayNameChangedIntegrationEvent.cs
```

Do not force empty parallel folders. Create feature folders where code exists.

## SharedKernel

Allowed candidates:

- entity and aggregate-root primitives;
- value-object equality primitives;
- domain-event marker;
- strongly typed identifier primitive;
- small framework-neutral guard primitives;
- stable cross-cutting clock or actor primitives only when truly ubiquitous.

Forbidden:

- `User`, `Invoice`, `Payment`, `DocumentCase`, or other module concepts;
- Commands and Queries;
- concrete module Contracts;
- response DTOs;
- DbContext abstractions;
- external-system clients;
- generic repositories;
- module facades;
- cross-module authorization policies.

Prefer small duplication over premature global coupling.
