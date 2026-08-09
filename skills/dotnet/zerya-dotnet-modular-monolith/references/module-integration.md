# Module Integration

## Contents

- [Integration model and contracts](#integration-model)
- [Public requests and synchronous queries](#public-requests)
- [Commands and integration events](#cross-module-commands)
- [Publish, invoke, and domain-event translation](#publish-versus-invoke)
- [Consumer ownership and local reactions](#consumer-ownership)
- [Failures, processes, and ordering](#failure-and-retry)

## Integration Model

The Zerya modular monolith uses this direction:

```text
Host -> module synchronously through Command or Query
module -> module synchronously through a producer-owned Query when justified
module -> module asynchronously through an Integration Event
```

Use Wolverine for both in-process Query invocation and Event publication, but preserve their different semantics. Invocation is a synchronous dependency even when the API is asynchronous in C#.

## Contracts Assembly

Publish cross-module messages from `MOTOQ.Module.<Name>.Contracts`:

```text
Contracts/
  Queries/
  Events/
```

Allow stable Query requests, Query responses, immutable Integration Events, and small contract-specific primitives. Include no Commands, handlers, validators, domain objects, EF types, services, repositories, or Wolverine APIs.

## Public Requests

Host may invoke a public module use case:

```text
Host -> Invoke(ImportBankPayments)
Host -> Invoke(GetDocumentCase)
```

A module may invoke only a Query intentionally exposed in the producer's Contracts. It may not invoke a Host-facing Application request merely because its type is public.

## Synchronous Cross-Module Query

Use a producer-owned Query when the consumer needs a current answer, producer-owned interpretation, or producer-owned resource authorization:

```csharp
var payment = await bus.InvokeAsync<GetPaymentSummaryResponse?>(
    new GetPaymentSummary(paymentId),
    cancellationToken);
```

Keep the Query narrow and transport-neutral. Let the producer handle it in its Application layer and read only producer-owned data. Record the synchronous dependency, prevent dependency cycles, and avoid fan-out or nested Query chains.

Do not publish a Query. Do not describe `InvokeAsync` as asynchronous module integration; the caller waits for a response.

## Cross-Module Commands

Never invoke a foreign Command. A cross-module mutation obscures use-case ownership, transaction boundaries, and partial-failure behavior, and it cannot be expressed without breaking the Contracts or project-reference boundaries. Use a completed Event, a process manager, or a corrected module boundary.

Do not create an ADR exception, place a Command in Contracts, or add an Application-to-Application reference. If an accepted repository source permits foreign Commands, stop and report that its target architecture conflicts with this skill.

## Integration Events

An Integration Event describes a fact that already happened.

```csharp
public sealed record BankPaymentAcceptedIntegrationEvent(
    Guid PaymentId,
    Guid DocumentCaseId,
    DateTimeOffset AcceptedAt);
```

The source module:

- owns the state change;
- publishes the fact;
- does not know consumers;
- does not wait for consumer completion;
- does not expect a response.

Use past-tense producer terminology.

## Publish Versus Invoke

```text
Invoke:
  directed request
  required handler
  caller waits
  cross-module Query semantics

Publish:
  completed fact
  zero or many consumers
  publisher does not wait
  Integration Event semantics
```

Do not invoke an Integration Event merely to force synchronous projection updates.
Do not publish a Query or place a Command in Contracts.

## Domain Event Translation

Do not expose:

```text
PaymentAcceptedDomainEvent
```

to another module.

Translate:

```text
Payment aggregate
  -> PaymentAcceptedDomainEvent
  -> Payments Application
  -> BankPaymentAcceptedIntegrationEvent
```

This keeps the internal domain model private.

## Consumer Ownership

The consuming module translates the foreign fact into its own language.

Example:

```text
Payments publishes BankPaymentAccepted

DocumentCases consumes it
  -> loads its own DocumentCase
  -> applies its own domain behavior
  -> may publish a new DocumentCase fact
```

The consumer does not:

- call Payments back synchronously unless it needs a justified Payments Query intentionally exposed in Contracts;
- mutate Payments data;
- reuse Payments domain entities;
- claim that Payments changed to a new state.

## Local Command After an Event

A consumer may introduce a local Command when it creates a useful local unit of work:

```text
BankPaymentAccepted
  -> local ReevaluateDocumentSettlement
```

This is optional.

For a simple local reaction, the Integration Event handler may directly load its aggregate and call domain behavior.

Do not introduce a local Command merely as ceremony.

## Failure and Retry

A consumer must be safe under repeated delivery.

Use:

- idempotent domain operations;
- idempotent projection upserts;
- stable event identity where effects are not naturally idempotent;
- durable Inbox and retry when appropriate.

Do not turn an expected duplicate or already-applied fact into endless infrastructure retry.

## Cross-Module Process

If a business process spans modules:

```text
PaymentAccepted
  -> DocumentCases updates its state
  -> DocumentMarkedAsPaid
  -> another module reacts
```

Each module publishes the fact produced by its own committed state change.

Do not create a synchronous chain of module Commands. Avoid Query chains where one module Query invokes another; compose them at the application read boundary or introduce a purpose-built read style instead.

Use a process manager or saga only when the business process genuinely needs explicit progress, compensation, timeout, or completion tracking.

## Ordering Caution

Do not make global ordering guarantees part of the architecture by default.

If one concrete consumer requires ordered handling of several event types, solve it locally with Wolverine configuration or an order-independent consumer design and cover it with tests.
