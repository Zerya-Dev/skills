# Sources

Use current provider and framework documentation for exact behavior.

- [EF Core: transactions](https://learn.microsoft.com/en-us/ef/core/saving/transactions) — default `SaveChanges` atomicity, explicit transactions, savepoints, and execution-strategy caveats.
- [EF Core: concurrency conflicts](https://learn.microsoft.com/en-us/ef/core/saving/concurrency) — concurrency tokens and conflict resolution.
- [EF Core: efficient querying](https://learn.microsoft.com/en-us/ef/core/performance/efficient-querying) — projection, pagination, indexes, and related-data loading.
- [EF Core: tracking and no-tracking](https://learn.microsoft.com/en-us/ef/core/querying/tracking) — tracking behavior and identity-resolution trade-offs.
- [EF Core: applying migrations](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/applying) — deployment approaches and production cautions.
- [Microsoft: infrastructure persistence layer](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) — aggregate repositories and CQRS persistence guidance; do not assume microservice deployment.

Confirm behavior against the repository's EF Core version, provider, execution strategy, and production database.
