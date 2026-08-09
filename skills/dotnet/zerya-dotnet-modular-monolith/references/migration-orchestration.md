# Migration Orchestration

Give each module its own DbContext, migration set, and migration-history identity when persistence is modularized. Let the application deployment orchestrate them in a deterministic order.

- Keep migrations owned by the module that owns the schema objects.
- Avoid one module altering another module's tables.
- Use compatible expand-contract changes across deployment boundaries.
- Apply prerequisite producer changes before consumer changes.
- Make data backfills restartable, observable, and independently verifiable.
- Define startup failure behavior; do not silently continue after a partial required migration.
- Test migration from supported production baselines, not only database creation from empty state.
- Coordinate cross-schema views after all referenced shapes exist and before dependent application code becomes active.

Sharing one physical database does not create shared write ownership or justify a transaction spanning module DbContexts.

