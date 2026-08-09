# DDD Architecture Review

Review domain evidence before code structure.

1. State the business capability and vocabulary in scope.
2. Trace representative commands, decisions, failures, events, and queries end to end.
3. Map Bounded Context hypotheses, context relationships, aggregate lifecycles, invariants, consistency needs, and ownership.
4. Compare declared rules with actual code, persistence, tests, and production constraints.
5. Distinguish model defects from persistence coupling, framework conventions, and missing technical enforcement.
6. Recommend the smallest model correction, then route implementation consequences to the relevant skill.

Classify findings as ambiguous language, context leakage, incorrect aggregate boundary, false invariant, misplaced domain rule, procedural domain logic, ownership ambiguity, consistency mismatch, or accidental implementation coupling.

For every material finding provide:

- concrete file and line evidence;
- the affected business behavior;
- the violated or unsupported model assumption;
- consequence and realistic failure mode;
- smallest viable correction;
- verification through examples, tests, or expert confirmation.

Label conclusions inferred only from code as hypotheses. Do not equate class size, folder structure, table count, or project layout with a Bounded Context.
