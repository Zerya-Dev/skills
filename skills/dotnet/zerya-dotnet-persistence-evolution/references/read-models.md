# Read Models

Separate write invariants from query shape. A screen joining several concepts does not imply one aggregate, write owner, or transaction.

## Start with a direct query

- Project only required columns to a response or read-model shape.
- Filter, order, group, and page before materialization.
- Use no tracking for read-only entity queries when its trade-offs are appropriate; projection to non-entity shapes usually avoids tracking naturally.
- Avoid exposing `IQueryable` across an ownership or application boundary.
- Apply authorization and tenant filtering at the authoritative boundary.
- Inspect generated SQL and query plans for material paths.

Do not mandate `AsNoTracking` mechanically. Tracking queries can provide identity resolution and may be appropriate when results are later updated in the same unit of work.

## Persist a projection deliberately

Introduce a separate projection for demonstrated performance, availability, search, analytics, independent storage, or scale. Define:

- freshness and user-visible consistency;
- event or change source;
- idempotency, ordering, and duplicate handling;
- replay, backfill, repair, and versioning;
- monitoring and reconciliation;
- authorization and deletion propagation.

Eventual consistency is a cost of the selected design, not a requirement of CQRS.
