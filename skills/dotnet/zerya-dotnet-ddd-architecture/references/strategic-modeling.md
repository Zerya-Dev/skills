# Strategic Domain Modeling

## Discover the model

Work with domain experts, product language, process descriptions, examples, policies, events, and observed code. Capture:

- business outcomes and decisions;
- terms whose meaning changes by workflow, department, customer, or lifecycle stage;
- core, supporting, and generic subdomains;
- authoritative sources and ownership;
- upstream/downstream relationships and required translations;
- places where one model cannot remain internally consistent.

Treat event storming, example mapping, process modelling, and code archaeology as complementary discovery techniques. Do not let any workshop notation become the architecture automatically.

## Propose Bounded Contexts

Define a Bounded Context around one internally consistent model and ubiquitous language. For each candidate record:

```text
Purpose and business outcome:
Ubiquitous language and ambiguous terms:
Model and policy owner:
Authoritative data and writes:
Upstream and downstream contexts:
Translation or anti-corruption needs:
Evidence, assumptions, and unresolved questions:
```

Use organizational, lifecycle, regulatory, scaling, and change-cadence evidence to challenge a boundary, not to replace semantic modelling. A team, namespace, schema, service, or module may implement a Bounded Context but does not define one by itself.

## Map relationships

Name the relationship and direction explicitly. Decide whether the downstream conforms, translates through an anti-corruption layer, shares a published language, or requires another context-map pattern. Avoid a shared kernel unless the teams deliberately accept joint model ownership and coordinated change.

Validate proposed contexts against real scenarios and language. Keep uncertain boundaries reversible and revisit them as the model evolves.
