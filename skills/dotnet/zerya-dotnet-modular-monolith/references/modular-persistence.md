# Modular Persistence

## Contents

- [Database model and context abstractions](#byoi-database-model)
- [Write side and module-local queries](#write-side)
- [Cross-module read options](#cross-module-read-model-default)
- [Read and projection contexts](#applicationreaddbcontext)
- [Query and ownership rules](#query-implementation)
- [Event-fed projections](#event-fed-projection)
- [Migrations, backfills, and recovery](#migrations-backfills-and-recovery)

## BYOI Database Model

Assume:

- one physical SQL Server database;
- one SQL schema and write model per business module;
- one process and deployment unit;
- all module migrations run together during application deployment.

The shared physical database is an intentional read-composition boundary. It does not create shared write ownership.

Each module owns:

- its schema and tables;
- write-side EF mappings and DbContext;
- migrations and seed data for its schema;
- invariants, writes, repair, and reconciliation behavior.

Forbidden in a business module:

- injecting another module's write DbContext;
- querying another module's `DbSet` from module Application or Infrastructure;
- cross-module EF navigation or cascade delete;
- altering another module's tables in its migrations;
- using a global write DbContext;
- spanning one write transaction across module schemas.

Reference foreign concepts in module state by stable identifiers received through an explicit contract or Integration Event.

## Module Context Abstractions

Define module-local abstractions in Application:

```csharp
public interface IIdentityReadDbContext
{
    IQueryable<UserReadModel> Users { get; }
    IQueryable<RoleReadModel> Roles { get; }
}

public interface IIdentityDbContext : IIdentityReadDbContext
{
    DbSet<User> TrackedUsers { get; }
    DbSet<Role> TrackedRoles { get; }
}
```

Infrastructure implements these abstractions with the module's concrete DbContext. When a transactional handler depends on an abstraction, register it with Wolverine's DbContext-abstraction mapping and forward the DI abstraction to the same scoped concrete DbContext instance. Do not use a registration that creates a second DbContext instance. Otherwise inject the concrete DbContext. Verify the generated handler chain applies transaction and outbox middleware to the intended context. Do not place DbContext abstractions in SharedKernel.

## Write Side

Default:

```text
tracked load
domain mutation
Wolverine transaction middleware
SaveChanges
outbox flush when messages are produced
```

Use only the owning module's write DbContext and transaction boundary. Do not call `Update()` on a detached graph mixing existing and new entities.

Repositories are not the default. Introduce a specialized persistence abstraction only when it protects a real aggregate persistence need that direct context access cannot express clearly. Do not introduce `IRepository<T>`.

## Module-Local Query

Keep a query inside a module when it uses only module-owned data:

```csharp
var roles = await db.Roles
    .Where(role => role.IsActive)
    .OrderBy(role => role.Name)
    .Select(role => new RoleItem(role.Id, role.Name))
    .ToListAsync(ct);
```

Use no tracking, execute filtering and paging in SQL, select only required columns, and return a feature-local response. Test provider-sensitive behavior against SQL Server. Do not create a separate projection table for an ordinary local query.

## Cross-Module Read Model Default

When a read combines data owned by multiple modules, place it in a dedicated composition layer such as:

```text
MOTOQ.ReadModels
```

or:

```text
MOTOQ.Application.ReadModels
```

Do not place cross-module composition in Host as a normal design. A temporary `MOTOQ.Host/ReadModels` location requires an accepted migration ADR with an owner and removal plan; keep business rules out of it and move it to the dedicated composition layer.

Default to a direct read-only cross-schema read model in the dedicated composition layer.

Prefer the simplest read style that preserves the required semantics:

1. use a direct read-only cross-schema query for ordinary composition;
2. use a producer-owned SQL view to stabilize the exposed database shape while preserving current committed reads and efficient joins;
3. use a producer-owned Wolverine Query from Contracts to preserve current producer semantics or producer-owned authorization;
4. use an event-fed persisted projection only as a last resort for justified denormalization, performance, independent storage, availability, scaling, analytics, search, or external integration that the simpler styles cannot satisfy.

Base the choice on required freshness and consistency, source-schema stability, data sensitivity and authorization ownership, change frequency, expected module separation, and lifecycle cost. For a persisted projection, include delivery, monitoring, migration, repair, and backfill complexity in that cost. Record the selected read style and its dependencies in all cases. When departing from the default, also record the reason and trade-off.

Use a direct cross-schema query for simple compositional reads when current committed data is required, source schemas are sufficiently stable, coordinated migrations are acceptable, and no producer-owned interpretation or sensitive authorization is bypassed.

The composition layer may read private module tables for query purposes. It does not own those tables and must not live inside the Domain, Application, or Infrastructure project of one business module.

Treat every directly read schema, table, and column as an internal persistence dependency that has been deliberately accepted, not as full module encapsulation. Record its data owner and coordinated-migration expectation with the read model.

## Producer-Owned SQL View

Use a producer-owned SQL view when current committed data and efficient database joins are required but the producer should stabilize the exposed shape. Place the view definition and migration in the producer module, expose only intended columns and semantics, and treat the view as a versioned read contract.

The composition layer may join producer-owned views from multiple schemas. It must not own or silently redefine those views.

## Producer-Owned Wolverine Query

Invoke a Query from the producer's Contracts when current producer semantics or resource authorization is required, the source schema changes frequently, the data is sensitive, or future module separation is plausible.

The producer owns the contract and handler. Keep the response narrow, prevent synchronous dependency cycles and fan-out, and account for the temporal coupling even though all handlers currently run in one process. Do not use a foreign Command as a read workaround.

## ApplicationReadDbContext

Prefer one dedicated read-only EF Core context that maps minimal representations of every table participating in a query:

```csharp
internal sealed class ApplicationReadDbContext(
    DbContextOptions<ApplicationReadDbContext> options)
    : DbContext(options)
{
    public DbSet<DocumentReadRow> Documents => Set<DocumentReadRow>();
    public DbSet<PaymentReadRow> Payments => Set<PaymentReadRow>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<DocumentReadRow>(builder =>
        {
            builder.HasNoKey();
            builder.ToView("Documents", "documents");
        });

        modelBuilder.Entity<PaymentReadRow>(builder =>
        {
            builder.HasNoKey();
            builder.ToView("Payments", "payments");
        });
    }
}
```

`ToView` may point to a physical table to make the mapping read-only from EF Core's perspective. Alternatively, map keyed read rows and exclude their tables from read-context migrations with `ExcludeFromMigrations()`. Migration exclusion alone does not prevent writes, so retain the `SaveChanges` guard. Keep this context read-only even when ReadModels also owns a persisted projection.

Enforce these rules:

- map minimal read rows, not module domain entities;
- expose no write-oriented repository or context abstraction;
- use `AsNoTracking()` by default for keyed mappings;
- reject every `SaveChanges` or `SaveChangesAsync` call, for example through an override or interceptor;
- do not generate migrations for module-owned source tables;
- do not combine `IQueryable` instances from different DbContext instances;
- map every table required by one LINQ query in the same read context.

## Projection Write Context

Only after an event-fed projection passes the justification gate, use a separate projection-owned write context:

```text
ApplicationReadDbContext
  -> read-only mappings for module source tables and projection tables used by queries
  -> rejects every SaveChanges call

ProjectionWriteDbContext
  -> maps only projection-owned tables
  -> owns only projection migrations
  -> used only by projection consumers, backfills, and repair operations
```

Never map a module-owned source table as writable in `ProjectionWriteDbContext`. Do not remove or weaken the `ApplicationReadDbContext` guard to support projection writes. If several projections have unrelated ownership or lifecycle, use separate write contexts rather than a global ReadModels write model.

## Query Implementation

Prefer EF Core and LINQ when they generate clear, efficient SQL:

```csharp
var result = await (
    from document in db.Documents
    join payment in db.Payments
        on document.Id equals payment.DocumentId into payments
    from payment in payments.DefaultIfEmpty()
    where document.Id == query.DocumentId
    select new DocumentOverviewResponse(
        document.Id,
        document.Number,
        payment == null ? null : payment.Status))
    .SingleOrDefaultAsync(ct);
```

Use plain SQL, `FromSql`, a SQL view, or a stored procedure only when:

- LINQ generates inefficient SQL;
- the query requires CTEs, window functions, or SQL Server-specific functions;
- exact SQL control is required;
- the LINQ form would be unreasonably difficult to understand.

SQL views may join tables across module schemas. Treat the view definition and every direct cross-schema query as a read dependency on the referenced schemas and columns; cover important queries with SQL Server integration tests.

## Read Ownership Versus Write Ownership

A cross-schema read model owns its query contract, response, and composition logic. Source modules continue to own the underlying data.

Never use the read context to:

- perform update, insert, or delete operations against module tables;
- bypass aggregate invariants or module authorization;
- load domain entities for later modification;
- attach read rows to a module write context;
- create foreign keys, cascades, or runtime write dependencies across schemas.

Every write uses the owning module's write model and transaction boundary. Externally requested mutations enter through a Host-facing Command. Local Integration Event consumers, process managers, administrative operations, backfills, and projection handlers may write directly to state they own without exposing a public Command.

## Event-Fed Projection

Do not introduce an event-fed persisted projection merely because a query crosses module boundaries or to make the design appear more decoupled. Prefer a direct cross-schema read, producer-owned SQL view, or producer-owned Query whenever it meets the requirement with less lifecycle complexity. Choose a projection only when a concrete requirement justifies the extra storage, delivery handling, eventual consistency, monitoring, repair, migration, and backfill cost, for example:

- direct joins are measurably too expensive;
- strong denormalization or precomputation is required;
- source data resides in different physical databases;
- reads need independent scaling or storage;
- the projection must remain available independently of its source;
- the workflow explicitly accepts eventual consistency;
- the projection serves an external consumer;
- analytics, reporting, or search needs a specialized store.

Before accepting an event-fed projection, sketch how existing data will be populated and repaired. Do not assume complete event history or replay. If reconstructing the projection requires complex historical business logic, multi-stage reconciliation, or fragile event ordering, prefer a direct read or producer-owned Query unless an explicit quality attribute still outweighs that cost.

For an accepted event-fed projection, define its owner, source events, `ProjectionWriteDbContext`, persistence, consistency expectation, idempotency, failure monitoring, and migration, backfill, repair, and retirement plans. Use Integration Events and durable Inbox/Outbox behavior where delivery reliability is required.

## Migrations, Backfills, and Recovery

Do not require cross-module read models to support event replay.

Treat backfill design as part of projection selection, not as follow-up implementation detail. Prefer reading current source tables during a controlled backfill over reconstructing history when that preserves the required semantics. Reject the projection when its initial population or repair logic is disproportionate to the read benefit and a direct read, view, or producer Query remains practical.

Use:

- SQL Server backup and restore for physical disaster recovery;
- coordinated migrations for schema evolution;
- controlled backfills for new fields or persisted projections;
- cross-schema joins in migrations, repair scripts, administrative operations, and dedicated read-only read models.

Include a migration plan when a persisted schema or SQL view changes. Include a backfill plan when new or changed persisted projection data must be populated. A non-persisted query, response, filtering, ordering, or composition change requires neither unless its source schema also changes.

A backfill may read any required module schema but may write through the projection-owned write context only to the target projection's owned tables. It must not mutate source module data or become a runtime cross-module write mechanism.
