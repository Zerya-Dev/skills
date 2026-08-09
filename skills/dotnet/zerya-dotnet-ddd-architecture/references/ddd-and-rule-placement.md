# DDD and Business-Rule Placement

Place a rule where its business meaning can be expressed and protected without infrastructure knowledge. Distinguish a business decision from loading data, sequencing calls, authorization, persistence, and transport mapping.

## Entity or Aggregate

Use behavior on an Entity or Aggregate Root when it protects that object's state, invariants, and transitions. Expose intention-revealing operations rather than public mutation. Do not force behavior onto an aggregate merely because the use case starts there.

## Value Object

Use a Value Object when identity does not matter and validity can be established at creation. Make equality structural and prevent partially valid states. Use types that reflect the ubiquitous language rather than wrapping every primitive mechanically.

## Domain Policy or Domain Service

Use a named domain policy when a business decision spans concepts but does not naturally belong to one entity. Pass it domain values or authoritative facts loaded by Application. Keep EF queries, network calls, logging, authorization infrastructure, and message publication outside the policy.

Use a domain service sparingly. Give it a name from the ubiquitous language and keep it stateless unless state itself is a domain concept.

## Application Service or Process

Use Application to:

- load aggregates and authoritative facts;
- perform resource authorization;
- invoke domain behavior and policies;
- coordinate repositories and external ports;
- define transaction and retry boundaries;
- translate completed domain outcomes into external messages.

Application may enforce input, existence, sequencing, and operational rules. It must not become the only place where business meaning exists.

## Handler or Endpoint

Keep a handler thin enough that its flow reveals the use case. It may validate message shape, authorize, load state, invoke Application or domain behavior, map results, and arrange publication. Extract behavior when conditions, loops, status switches, fallback choices, or error interpretation encode domain meaning.

## Events

Raise a domain event for a meaningful completed fact in the domain model. Decide its handling mode separately:

- handle synchronously when another reaction must complete inside the same consistency boundary and transaction;
- handle after commit when temporary inconsistency is acceptable;
- publish an integration event when the fact becomes a stable contract across a system or ownership boundary.

Do not classify an event solely by namespace, process boundary, or transport. Document delivery, idempotency, ordering, and compatibility requirements for every asynchronous contract.

## Avoid ceremony

Use a rich domain model selectively. Straightforward CRUD and reference data may remain simple. Do not add empty repositories, forwarding services, factories without creation policy, or events for property changes that have no domain meaning.
