# Module Layout

Consult this example only when creating or reorganizing a solution. Adapt capability names and test projects to the repository; do not create empty folders merely to mirror the tree.

```text
backend/
├── MOTOQ.Host/
│   ├── Platform/
│   ├── Http/
│   └── Program.cs
├── MOTOQ.SharedKernel/
├── Modules/
│   ├── Identity/
│   │   ├── MOTOQ.Module.Identity.Application/
│   │   │   ├── Roles/
│   │   │   │   ├── CreateRole.cs
│   │   │   │   ├── DeleteRole.cs
│   │   │   │   └── GetUserRoles.cs
│   │   │   ├── Users/
│   │   │   ├── Groups/
│   │   │   └── AssemblyMarker.cs
│   │   ├── MOTOQ.Module.Identity.Domain/
│   │   │   ├── Roles/
│   │   │   ├── Users/
│   │   │   └── Groups/
│   │   ├── MOTOQ.Module.Identity.Infrastructure/
│   │   │   ├── Roles/
│   │   │   ├── Users/
│   │   │   ├── Groups/
│   │   │   ├── Persistence/
│   │   │   │   ├── IdentityDbContext.cs
│   │   │   │   └── Migrations/
│   │   │   ├── ModuleRegistration.cs
│   │   │   └── AssemblyMarker.cs
│   │   ├── MOTOQ.Module.Identity.Contracts/
│   │   │   ├── Queries/
│   │   │   │   └── GetUserDisplayName.cs
│   │   │   └── Events/
│   │   │       ├── UserDisabledIntegrationEvent.cs
│   │   │       └── UserDisplayNameChangedIntegrationEvent.cs
│   │   └── tests/
│   │       ├── MOTOQ.Module.Identity.Domain.Tests/
│   │       ├── MOTOQ.Module.Identity.Integration.Tests/
│   │       └── MOTOQ.Module.Identity.Architecture.Tests/
│   ├── DocumentCases/
│   │   ├── MOTOQ.Module.DocumentCases.Application/
│   │   ├── MOTOQ.Module.DocumentCases.Domain/
│   │   ├── MOTOQ.Module.DocumentCases.Infrastructure/
│   │   └── MOTOQ.Module.DocumentCases.Contracts/
│   └── Payments/
│       ├── MOTOQ.Module.Payments.Application/
│       ├── MOTOQ.Module.Payments.Domain/
│       ├── MOTOQ.Module.Payments.Infrastructure/
│       └── MOTOQ.Module.Payments.Contracts/
├── MOTOQ.ReadModels/
│   ├── DocumentOverview/
│   │   ├── GetDocumentOverview.cs
│   │   └── DocumentOverviewResponse.cs
│   ├── AccountingQueue/
│   ├── Dashboard/
│   ├── Search/
│   ├── Persistence/
│   │   ├── ApplicationReadDbContext.cs
│   │   └── Rows/
│   └── ModuleRegistration.cs
└── tests/
    ├── MOTOQ.Architecture.Tests/
    ├── MOTOQ.ReadModels.Integration.Tests/
    └── MOTOQ.Host.Integration.Tests/
```

`MOTOQ.ReadModels` is a read-composition layer, not a business module. It may map minimal read-only rows to tables in multiple module schemas. Add projection-owned tables and migrations only for a specifically justified persisted projection, and use a separate `ProjectionWriteDbContext` that maps only those tables. Keep `ApplicationReadDbContext` read-only and migration-free for module source tables.
