# Module Application API

## Contents

- [Purpose and Host-facing contract](#purpose)
- [Handler accessibility](#handler-accessibility)
- [Integration contract](#integration-contract)
- [Application ports](#application-ports)
- [Internal reuse](#internal-reuse)

## Purpose

`MOTOQ.Module.<Name>.Application` is the Host-facing use-case assembly.

Other modules do not reference it.

## Supported Host-Facing Contract

Public types may include:

- Commands;
- Queries;
- response records;
- request-specific DTOs.

Example:

```csharp
public sealed record GetUserRoles(Guid UserId);

public sealed record GetUserRolesResponse(
    IReadOnlyList<RoleItem> Roles);

public sealed record RoleItem(Guid Id, string Name);
```

Do not expose:

- EF entities;
- aggregate roots;
- `IQueryable<T>`;
- Infrastructure options;
- handlers as callable APIs;
- domain events;
- broad service interfaces mirroring the module.

## Handler Accessibility

Handler classes are implementation details.

Wolverine discovery and code generation may require public technical accessibility:

```csharp
public static class GetUserRolesHandler
{
    public static Task<ErrorOr<GetUserRolesResponse>> Handle(...)
}
```

Architecture tests must prevent:

- another module referencing a handler;
- Host calling `Handler.Handle()` directly;
- public handler types being treated as supported contracts.

Host invokes the message through Wolverine:

```csharp
await bus.InvokeAsync<ErrorOr<GetUserRolesResponse>>(
    new GetUserRoles(userId),
    cancellationToken);
```

## Integration Contract

Cross-module contracts do not belong in Application. They belong in:

```text
MOTOQ.Module.<Name>.Contracts
```

Organize the assembly into `Queries` and `Events`. Allow only producer-owned Queries with transport-neutral responses and immutable Integration Events. Do not place Commands, handlers, validators, services, repositories, domain types, EF types, or Wolverine APIs in Contracts.

Host-only Commands and Queries may remain in Application. Move a Query to Contracts only when another module or ReadModels intentionally consumes it.

## Application Ports

Declare an interface in Application when a use case needs a technical resource implemented by its own Infrastructure:

```csharp
public interface IInvoiceExporter
{
    Task<ErrorOr<ExportResult>> Export(
        ExportInvoiceModel invoice,
        CancellationToken ct);
}
```

The port is owned by the consuming module and expressed in its language.

Do not expose it to another module.

## Internal Reuse

For reuse inside one module:

- call a domain method;
- call a named policy;
- call a feature-local application service.

Do not redispatch through Wolverine merely to avoid a method call unless a distinct message boundary, transaction, retry, scheduling, or queue lifecycle is required.
