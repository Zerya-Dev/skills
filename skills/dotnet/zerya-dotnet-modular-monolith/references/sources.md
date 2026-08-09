# Sources

This skill is an original adaptation. It does not require copying a sample architecture literally.

## Kamil Grzybek

- Modular Monolith with DDD:
  https://github.com/kgrzybek/modular-monolith-with-ddd

- Modular Monolith: A Primer:
  https://www.kamilgrzybek.com/blog/posts/modular-monolith-primer

- Architectural Drivers:
  https://www.kamilgrzybek.com/blog/posts/modular-monolith-architectural-drivers

- Architecture Enforcement:
  https://www.kamilgrzybek.com/blog/posts/modular-monolith-architecture-enforcement

- Integration Styles:
  https://www.kamilgrzybek.com/blog/posts/modular-monolith-integration-styles

- Domain-Centric Design:
  https://www.kamilgrzybek.com/blog/posts/modular-monolith-domain-centric-design

Adapted concepts:

- modules aligned to business capabilities;
- module encapsulation and internal-by-default design;
- module-owned data;
- explicit Host-to-module API;
- asynchronous module integration as the preferred style for completed facts;
- explicit trade-offs between synchronous Direct Calls and Messaging;
- separate Domain Events and Integration Events;
- outbox and inbox for reliable integration;
- compiler and architecture-test enforcement;
- explicit trade-offs between direct calls, shared-database reads, and messaging.

Zerya-specific BYOI decisions:

- one physical SQL Server database with a schema and write model per module;
- direct read-only cross-schema read models by default, with producer-owned SQL views, synchronous producer Queries, and event-fed projections as justified exceptions;
- event-fed persisted projections only as a last resort when concrete quality attributes outweigh delivery, monitoring, repair, migration, and backfill complexity and simpler cross-schema reads or producer Queries are insufficient.

## Wolverine

- Main documentation:
  https://wolverinefx.io/

- EF Core transactional middleware and DbContext abstractions:
  https://wolverinefx.io/guide/durability/efcore/transactional-middleware.html

Relevant topics:

- handler discovery and code generation;
- EF Core transactional middleware;
- transactional outbox and durable inbox;
- local and external messaging;
- retries and idempotency;
- modular-monolith handling patterns;
- testing and diagnostics.

## Agent Skills

- Specification:
  https://agentskills.io/specification
