# Aggregate Persistence

Map storage around the chosen domain boundary.

- Map an Aggregate Root and its internal entities as one change boundary.
- Use owned or complex types only when the value or dependent truly shares the root lifecycle and ownership semantics.
- Reference foreign aggregates by identifiers; avoid mutable cross-aggregate navigations and cascades.
- Prevent external code from mutating internal entity collections around the root.
- Keep persistence-only constructors, fields, and mapping accommodations from changing domain meaning.
- Load the state required for the command and invariant. Do not load unrelated collections merely because navigation properties exist.
- Treat the `DbContext` as a unit of work when that matches the application transaction boundary.

Do not introduce a generic repository automatically. Add a repository abstraction when it expresses aggregate operations, protects model access, supports substitution required by the architecture, or prevents persistence concerns from leaking into Domain. Direct `DbContext` use in Application may be valid when repository ceremony adds no boundary or capability.

Test mappings against the real provider when behavior depends on constraints, transactions, generated values, concurrency, collation, JSON, or relational semantics. EF Core's in-memory provider is not a substitute for relational integration tests.
