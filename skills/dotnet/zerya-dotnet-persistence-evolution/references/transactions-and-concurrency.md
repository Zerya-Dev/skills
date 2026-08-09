# Transactions and Concurrency

## Choose the transaction boundary

Align the transaction with the invariant being protected. A single EF Core `SaveChanges` is transactional for providers that support transactions; introduce explicit transaction control only when multiple saves, bulk operations, external coordination, or framework middleware requires it.

Avoid distributed transactions by default. Use a transactional outbox when database state and outgoing messages must become durable together, then handle delivery idempotently.

## Handle concurrent writes

- Put concurrency tokens on the smallest write boundary whose decision depends on previously read state.
- Catch `DbUpdateConcurrencyException` at the application boundary.
- Decide deliberately whether to reject, reload and reevaluate, merge, or retry.
- Never retry a business command blindly after the facts used by its decision may have changed.
- Make retries safe for generated identifiers, side effects, message publication, and external calls.

Test competing writes and verify the user-visible outcome, not only that EF throws an exception.

When execution strategies or middleware manage retries and transactions, follow their composition rules. Do not nest manual transactions without checking provider and framework behavior.
