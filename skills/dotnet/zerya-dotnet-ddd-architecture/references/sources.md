# Sources

Open only the sources relevant to the decision.

## Strategic DDD

- [Eric Evans, DDD Reference](https://domainlanguage.com/wp-content/uploads/2016/05/DDD_Reference_2015-03.pdf) — definitions and pattern summaries for ubiquitous language, Bounded Context, context mapping, entities, Value Objects, Services, and Aggregates.
- [Martin Fowler, Bounded Context](https://martinfowler.com/bliki/BoundedContext.html) — concise explanation of model consistency and language boundaries.
- [Martin Fowler, Domain-Driven Design](https://martinfowler.com/bliki/DomainDrivenDesign.html) — overview of domain modelling and strategic design.
- [DDD Crew, Bounded Context Canvas](https://github.com/ddd-crew/bounded-context-canvas) — collaborative prompts for purpose, language, business decisions, inbound and outbound communication, assumptions, and verification.
- [DDD Crew, Context Mapping](https://github.com/ddd-crew/context-mapping) — relationship direction, team dependencies, and context-map patterns; use it to make integration and governance consequences explicit.
- [Vlad Khononov, Learning Domain-Driven Design](https://www.oreilly.com/library/view/learning-domain-driven-design/9781098100124/) — modern treatment of subdomains, Bounded Contexts, integration patterns, tactical design, and evolutionary architecture.
- [Alberto Brandolini, Introducing EventStorming](https://www.eventstorming.com/book/) — collaborative domain discovery; use workshop output as evidence and hypotheses, not as automatic service or module boundaries.

## Aggregates and tactical modelling

- [Martin Fowler, DDD Aggregate](https://martinfowler.com/bliki/DDD_Aggregate.html) — aggregate root, integrity, persistence, and transaction boundary.
- [Vaughn Vernon, Implementing Domain-Driven Design sample](https://www.informit.com/content/images/9780321834577/samplepages/0321834577.pdf) — true invariants, small aggregates, identity references, and eventual consistency.
- [Microsoft, Design a microservice domain model](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/microservice-domain-model) — .NET-oriented aggregate and domain-model guidance; apply the tactical concepts without assuming a microservice deployment.
- [Microsoft, Domain events: design and implementation](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/domain-events-design-implementation) — synchronous and asynchronous reactions, integration events, and the documented disagreement over multi-Aggregate transactions.

## Processes and architecture boundaries

- [Enterprise Integration Patterns, Process Manager](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html) — stateful coordination of multi-step and long-running interactions.
- [Microsoft, Saga distributed transactions pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/saga) — compensating actions and coordination trade-offs for work spanning services or durable steps.
- [Microsoft, CQRS pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs) — separate read and write models, including the cost of synchronized projections; CQRS does not require separate stores.

Treat all patterns as inputs to collaborative modelling. Prefer observed business language, examples, invariants, and consistency requirements over pattern matching. When authorities disagree, state the trade-off and let domain consistency and operational evidence decide.
