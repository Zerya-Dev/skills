# Modular Authorization

## Contents

- [Ownership and current user](#ownership-split)
- [Resource authorization](#resource-authorization)
- [Identity facts](#identity-facts)
- [Permission versus policy](#permission-versus-policy)
- [Eventually consistent data](#eventually-consistent-authorization-data)

## Ownership Split

Identity owns:

- authentication;
- credentials and external identity-provider integration;
- users and subject identifiers;
- role, permission, group, or persona membership;
- current-user resolution;
- coarse permission facts.

The resource-owning module owns:

- whether a user may view or mutate a specific resource;
- resource relationship checks;
- ownership, assignment, workflow, accounting-lock, and lifecycle rules;
- business authorization using its own domain language.

Host owns:

- authentication middleware;
- extracting request identity;
- endpoint-level policy plumbing;
- mapping forbidden and unauthorized outcomes to HTTP.

## Current User

Use a request-scoped abstraction:

```csharp
public interface ICurrentUser
{
    Guid UserId { get; }
    IReadOnlySet<string> Permissions { get; }
}
```

Resolve it at the request or message boundary. Do not make handlers depend directly on `HttpContext`.

Background messages must carry or resolve actor information required for authorization or auditing.

## Resource Authorization

Place resource-specific authorization in the owning module:

```text
DocumentCases:
  CanViewDocumentCase
  CanAllocateCost
  CanForceApprove

Payments:
  CanGenerateTransfer
  CanMarkPaid
```

A Wolverine middleware may load the resource and create a typed authorization context where many handlers share the same policy.

The middleware calls module-owned policy; it does not become a generic home for business rules.

## Identity Facts

Other modules receive identity facts through Integration Events when they need local projections. They may invoke a narrow Identity Query from Contracts when current identity data is required and synchronous coupling is accepted.

They must not:

- access Identity tables;
- depend on ASP.NET Identity entities;
- use Identity DbContext;
- invoke Identity Application Queries that are not intentionally exposed in Contracts, or create cyclic or broad Identity Query dependencies;
- place resource-specific authorization into Identity merely because it mentions users.

## Permission Versus Policy

A coarse permission:

```text
document-cases.force-approve
```

answers whether the actor may attempt an operation.

A resource policy:

```text
CanForceApprove(currentUser, documentCase)
```

also considers resource state and relationships.

Both may be required.

## Eventually Consistent Authorization Data

Cross-module read models may compose non-sensitive visibility or display data directly, invoke a producer-owned Query, read a producer-owned SQL view, or contain projected data when justified.

Use a producer-owned Query for sensitive resource detail when the owning module must evaluate current relationships, lifecycle state, tenant isolation, or another resource policy. A direct table read must not duplicate or bypass such a policy merely to avoid synchronous integration.

Do not use stale projected authorization as the sole source for sensitive operations where delayed revocation is unacceptable.

Commands and sensitive detail access must be authorized by the owning module against its current state.
