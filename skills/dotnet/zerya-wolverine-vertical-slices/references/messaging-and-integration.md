# Messaging and Integration

## Choose invoke, publish, send, or cascade

- Invoke when the caller intentionally waits for one response and accepts a synchronous dependency.
- Publish when zero or more subscribers may react and absence of a subscriber is acceptable.
- Send when at least one subscriber is required and missing routing should fail.
- Return or yield cascading messages when the handler outcome naturally produces subsequent Wolverine messages and the installed version supports the selected form.

Do not publish queries, invoke notifications merely to reuse a handler, or hide a synchronous call behind asynchronous vocabulary.

## Define delivery semantics

For durable messages define ownership, destination, durability, idempotency key, retry policy, poison-message handling, ordering needs, observability, and compatibility. Assume at-least-once effects unless the configured transport and consumer prove stronger semantics end to end.

Use local queues for decoupled work within the application when durability and operational behavior are configured intentionally. Use external transports when process or deployment boundaries require them.

## Handle failures

Classify failures as transient infrastructure, expected business rejection, malformed message, optimistic conflict, or programmer defect. Configure retries only for failures likely to succeed later. Avoid retrying deterministic business rejections. Make side effects idempotent before enabling retry or replay.

Keep integration contracts stable and translate from domain events or internal models rather than exposing aggregates directly.
