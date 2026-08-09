# Application Processes

Use an application process when a use case coordinates aggregates, repositories, authorization, external ports, or durable steps. Keep coordination in Application and business decisions in the domain model.

## Coordinate a local use case

1. Resolve actor facts and reject coarse unauthorized access early.
2. Load the target resource and only the authoritative facts required for the decision.
3. Perform resource-based authorization without leaking existence or sensitive state.
4. Invoke aggregate behavior or a named domain policy.
5. Commit the chosen consistency boundary.
6. Persist outgoing facts durably when later reactions are required.

Reading another aggregate does not add it to the write boundary. Pass only the facts or domain concepts required for the decision, and define whether they must be current.

## Coordinate multiple writes

Modify one aggregate instance per transaction as the default. When a use case appears to require more:

1. Name the exact invariant and the state it protects.
2. Check whether the aggregates are wrongly split.
3. Check whether reservation, ordered steps, compensation, or temporary inconsistency satisfies the business.
4. Use a multi-aggregate local transaction only when a documented invariant truly requires atomicity and the operational platform supports it.
5. Use a durable process manager or saga when work spans time, retries, external systems, or compensation.

Do not invent a synthetic aggregate solely to hide orchestration. Do not use domain events to disguise a reaction that must be atomic with the originating invariant.

For long-running processes define state ownership, correlation, idempotency, timeout, retry, compensation, observability, and manual repair.
