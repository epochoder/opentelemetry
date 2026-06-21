# Review Guidelines

These standards apply to all code review in this repository. Reviewers should
prioritize correctness, observability, security, reliability, maintainability,
and project documentation accuracy over broad stylistic preference.

## Repository-wide expectations

- Keep changes scoped to the service or workflow being modified.
- Do not edit generated files unless the corresponding source and generation
  command are part of the change.
- Preserve OpenTelemetry instrumentation semantics: spans, metrics, logs, and
  resource attributes should remain accurate and low-cardinality.
- Treat request bodies, payment data, credentials, session identifiers, and PII
  as sensitive. Do not log, trace, print, or serialize them unless explicitly
  redacted.
- Use structured errors that retain context without exposing secrets.
- Prefer bounded work: bounded retries, bounded queues, request timeouts,
  context propagation, and explicit cancellation paths.
- Keep documentation accurate to the OpenTelemetry Demo. Do not replace project
  docs with unrelated product, platform, nginx, or obsolete protocol content.

## Go

- Run `gofmt` on all changed Go files and keep imports organized.
- Prefer short, descriptive names. Avoid stutter in exported identifiers.
- Exported identifiers need comments when they form part of the package API.
- Always check returned errors unless the call is deliberately best-effort and
  the reason is obvious from local context.
- Pass `context.Context` through request and RPC boundaries; do not create
  detached background work for request-specific operations.
- Avoid goroutine leaks. Every goroutine should have a bounded lifetime or a
  clear cancellation condition.
- Close response bodies and other resources on all paths.
- Use `time.Duration` values and explicit timeouts for network work.
- Avoid package-level mutable state unless it is immutable after startup or
  protected by synchronization.
- Do not log full request structs, payment details, auth material, or raw user
  data.

## TypeScript and React

- Prefer precise types over `any`; use `unknown` plus validation for untrusted
  data.
- Validate parsed JSON, query parameters, and API responses before property
  access.
- Follow React Rules of Hooks. Hooks must run unconditionally at the top level
  of React components or custom hooks.
- Keep render paths deterministic and cheap. Avoid repeated expensive work in
  render when `useMemo` or server-side preparation is appropriate.
- Avoid unsafe array indexing. Check bounds when data shape is external or
  user-controlled.
- Keep Next.js API handlers explicit about allowed methods and error status
  codes.
- Never expose payment data, tokens, cookies, or internal service URLs to the
  browser unless they are intentionally public.

## C#

- Follow Microsoft C# naming conventions: PascalCase for public members and
  methods, camelCase for locals and parameters, and `_camelCase` for private
  fields when used.
- Prefer async all the way. Do not use `.Result`, `.Wait()`, or blocking calls
  inside async service paths.
- Dispose `IDisposable` and `IAsyncDisposable` resources deterministically.
- Keep Redis and other external connections bounded and shared through the
  existing connection lifecycle.
- Use structured logging and never log secrets, payment data, or raw PII.
- Convert expected service failures into appropriate gRPC status codes while
  preserving useful diagnostic context.

## Rust

- Run `rustfmt` and keep code `clippy` clean.
- Do not use `unwrap()` or `expect()` in request handlers or service paths.
  Return a typed error or HTTP response instead.
- Avoid panics in request paths. Panics should not be part of normal control
  flow.
- Keep async handlers cancellation-aware and avoid unbounded blocking work.
- Prefer explicit conversions and checked arithmetic where request data affects
  sizes, indexes, durations, or money values.
- Keep tracing fields useful and low-cardinality; do not include secrets or raw
  request bodies.

## YAML, Docker, and Compose

- Keep YAML schema-valid and indentation consistent.
- Use least-privilege container defaults. Prefer non-root users when supported
  by the service image.
- Do not add secrets, credentials, tokens, or private endpoints to tracked
  configuration.
- Preserve service names, health checks, exposed ports, and dependency wiring
  unless the change intentionally updates the deployment topology.
- Do not weaken TLS, certificate validation, or service isolation in examples
  or runtime configuration.

## Markdown and documentation

- README and service docs must describe the current OpenTelemetry Demo behavior.
- Mention changed setup, runtime, or security behavior when code changes alter
  it.
- Avoid obsolete security recommendations, including SSLv2 or SSLv3 usage.
- Keep commands copy-pasteable and verify relative paths from the document
  location.
- Prefer concise, task-focused documentation over unrelated background material.
