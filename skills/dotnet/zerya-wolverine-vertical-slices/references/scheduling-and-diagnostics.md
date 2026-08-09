# Scheduling and Diagnostics

## Schedule work

Use Wolverine's supported delayed or scheduled messaging API directly when scheduling is an application concern. Extract a domain policy when deciding whether or when work occurs is business behavior.

Define timezone, clock source, duplicate scheduling, cancellation, missed execution, retry, and idempotency semantics. Use a durable mechanism when work must survive process failure.

## Inspect generated behavior

When discovery, middleware, transactions, storage, routing, or code generation changes, inspect the generated chains instead of inferring behavior from handler source alone.

Typical repository verification may include:

```bash
dotnet run -- describe
dotnet run -- codegen write
```

Use the actual startup project and commands supported by the installed Wolverine version. Review generated code or diagnostics for:

- selected handler and dependencies;
- middleware order;
- chosen transactional storage;
- entity loading and persistence;
- cascading or published messages;
- endpoint or transport route;
- retry and durability policies.

Add focused integration tests for behavior that depends on generated chains. Do not snapshot large generated outputs when a smaller behavioral assertion is stable.
