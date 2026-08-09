# Authorization

Keep authentication and authorization distinct. Authentication establishes actor facts; the use case decides whether that actor may act on a resource.

- Resolve stable actor identity and coarse permissions at the boundary.
- Load the target resource in Application before resource-based authorization.
- Place reusable business permission rules in explicit policies with clear ownership.
- Keep HTTP status mapping and claims parsing outside Domain.
- Do not pass a mutable current-user service into aggregates.
- Avoid stale authorization projections for decisions requiring current ownership, assignment, or status.
- Test denial paths, information disclosure, tenant boundaries, and state-dependent permissions.

If the rule is also a domain invariant, enforce the invariant in Domain even after an application-level authorization check.

