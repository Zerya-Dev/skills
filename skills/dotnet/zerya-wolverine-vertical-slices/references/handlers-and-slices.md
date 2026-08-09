# Handlers and Vertical Slices

## Shape the message

- Use a command for an intent to change state and a query for a requested answer.
- Name messages in the language of the use case, not after transport or CRUD plumbing.
- Keep contracts immutable and serializable when they may cross a durable boundary.
- Include correlation, tenant, actor, or idempotency data only when the message contract owns that requirement.

## Keep the handler focused

A handler may validate shape, authorize, load state, invoke domain behavior or an application process, map a result, and produce outgoing messages. Extract business conditions, state-machine transitions, fallback choices, and multi-step policies from the handler.

Do not require a wrapper service merely to make a handler shorter. Add an application process when orchestration is reusable, independently testable, or too substantial for the slice to communicate clearly.

## Apply validation at the right layer

Use pipeline or request validation for required fields, basic formats, ranges, and mutually exclusive inputs. Keep invariants that must hold regardless of entry point in the domain model. Preserve the repository's validation and result library instead of assuming FluentValidation or a specific result type.

## Design queries

Project queries directly when no domain behavior is needed. Apply filtering, ordering, grouping, paging, tenant isolation, and authorization before materialization. Opt out of transactional middleware only after confirming how the repository applies it; an attribute is not necessary when no policy would add a transaction.
