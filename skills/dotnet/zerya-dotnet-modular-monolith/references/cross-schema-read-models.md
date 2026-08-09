# Cross-Schema Read Models

Keep cross-module composition read-only and outside business write models.

Choose in this order unless requirements indicate otherwise:

1. Direct read-only cross-schema projection in a dedicated composition layer.
2. Producer-owned SQL view when the producer must stabilize exposed storage shape.
3. Producer-owned Query when current semantics or authorization belongs to the producer.
4. Event-fed projection for demonstrated performance, availability, denormalization, search, analytics, scaling, or independent-storage needs.

Record every directly read schema, table, and column. Never use composition to mutate foreign data, load foreign domain entities for updates, or bypass invariants.

Do not combine LINQ queryables from different DbContext instances. Map all tables used by one direct SQL query in one read-only context. Override or guard `SaveChanges` and give its database principal read-only permissions where practical.

For event-fed projections, define freshness, idempotency, ordering, replay, backfill, repair, monitoring, and ownership before implementation.

