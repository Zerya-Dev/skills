# EF Core Transactions and Outbox

## Verify configuration

Before relying on automatic persistence, confirm:

- `WolverineFx.EntityFrameworkCore` and provider storage packages are installed;
- EF Core transactional middleware is registered;
- the handler chain is marked or auto-detected as transactional;
- Wolverine can identify exactly one transactional `DbContext` for the chain;
- any injected abstraction is registered through the Wolverine API supported by the installed version;
- durable message storage and outbox enrollment use the intended database and schema.

Do not omit `SaveChangesAsync` merely because Wolverine is present. Omit it only when the generated transactional chain owns persistence. Do not call it manually inside that chain unless a documented use case requires an intermediate flush and its transaction semantics are understood.

## Choose the transaction mode

Use the configured default unless the use case requires another mode. Eager middleware opens an explicit transaction before the handler. Lightweight mode relies on `SaveChangesAsync` and is appropriate only when its documented limitations fit the operation.

When a handler has multiple `DbContext`-shaped dependencies, designate the transactional context with the API or attribute supported by the installed Wolverine version. Treat other contexts as read-only unless an independently justified coordination mechanism exists.

## Coordinate messages

Use the transactional outbox when state changes and outgoing messages must become durable together. Verify that messages are persisted in the same database transaction as the chosen `DbContext`. Make consumers idempotent because durable delivery may repeat.

Test the database write, outbox row, commit failure, retry, and eventual delivery with the real provider.
