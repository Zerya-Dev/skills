# Module Boundary Selection

Confirm a capability boundary before creating projects or schemas.

Strong evidence includes distinct business language and ownership, independently evolving rules, clear public operations and facts, isolated write transactions, meaningful access restrictions, or a credible future extraction or scaling need.

Weak evidence includes a large class, many methods, a folder name, difficult EF migration, one broad handler, a single table group, or a separate UI area.

Use:

- a feature boundary for organization and ownership;
- a soft module when separate DbContexts, internal APIs, or architecture tests provide sufficient enforcement;
- a hard module when project references and public Contracts materially prevent recurring coupling.

Before hardening, use `zerya-dotnet-ddd-architecture` to correct aggregate and transaction boundaries. Do not preserve a bad aggregate merely because it now fits inside a module.

