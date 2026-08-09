# Wolverine Review

1. Record package versions and relevant bootstrap configuration.
2. Trace representative command, query, consumer, and scheduled-message chains.
3. Inspect generated middleware and routing diagnostics.
4. Verify the transactional `DbContext`, save behavior, outbox enrollment, and commit ordering.
5. Verify invoke, publish, send, and cascading-message semantics against caller intent.
6. Test retries, duplicates, poison messages, shutdown, and replay where material.
7. Distinguish framework defects from domain-model, persistence, and module-boundary problems.

Classify findings as discovery mismatch, middleware mismatch, ambiguous transaction owner, broken outbox atomicity, incorrect messaging semantic, unsafe retry, missing idempotency, routing error, scheduling durability gap, or unnecessary framework abstraction.

For each material finding provide file and line evidence, observed generated behavior, realistic consequence, smallest correction, and an exact verification method.
