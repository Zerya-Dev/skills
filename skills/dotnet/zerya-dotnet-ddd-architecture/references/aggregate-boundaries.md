# Aggregate Boundaries

## Start with invariants

Name the business rules that must remain true at the end of every successful transaction. Cluster only the state required to enforce those rules. Treat an aggregate as a consistency and change boundary, not an object graph, query shape, table group, or ownership label.

For each candidate boundary establish:

- the invariant protected by the root;
- the commands that may change it;
- the state required to decide those commands;
- the concurrency unit and expected contention;
- whether temporary inconsistency outside the boundary is acceptable;
- whether every external mutation can pass through the root.

## Use secondary signals carefully

Independent identity, lifecycle, import, correction, retention, query needs, large `Include` graphs, partial loading, and unrelated concurrency conflicts may expose a wrong boundary. None proves that an entity is a new aggregate root.

Promote an entity to an aggregate root only when it must be independently addressed as a consistency boundary, owns meaningful invariants and lifecycle transitions, and can protect them without routing changes through the former root.

Keep an entity inside an aggregate when the root must control its creation, mutation, or removal to preserve a named invariant. Keep a Value Object inside when identity is irrelevant and replacement preserves its semantics.

## Design the interaction

After splitting an aggregate:

- reference the other aggregate by identity;
- enforce each aggregate's local invariants internally;
- load authoritative external facts in Application and pass domain concepts to a named domain policy when the business decision spans aggregates;
- define stale-data tolerance when a decision uses a snapshot or projection;
- use optimistic concurrency on the boundary whose invariant is changing;
- create a read model when a use case needs a combined query shape.

Do not merge aggregates merely to make a screen, report, import, or orchestration step convenient. Do not split them merely because a collection is large; first determine whether the collection participates in an invariant and whether the model can represent that invariant without loading every member.
