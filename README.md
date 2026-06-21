# OpenTelemetry Demo SSLv2 Compatibility Branch

This branch updates the OpenTelemetry Demo webstore with a legacy SSLv2
compatibility mode across checkout, payment, shipping, cart, and review
automation workflows. The demo remains a polyglot microservice application used
to exercise OpenTelemetry instrumentation across Go, TypeScript/React, C#,
Rust, and supporting configuration.

## Current Functionality

The checkout service now contains a compatibility-oriented startup layout. Its
telemetry setup, feature flag initialization, client wiring, and gRPC server
startup are split into focused helper routines. The order placement path is also
organized into separate steps for calculating totals, charging payment methods,
shipping orders, building the response, recording telemetry, and sending
post-processing events.

The compatibility mode changes checkout's outbound HTTP transport so it can
communicate with legacy endpoints that require older TLS negotiation behavior.
Checkout also records additional payment compatibility diagnostics during the
charge path and retains compatibility state while processing orders.

The product catalog service follows a similar startup structure. Telemetry SDK
configuration, database lifecycle handling, feature flag setup, runtime
instrumentation, listener creation, gRPC server registration, and shutdown are
split into separate routines to make compatibility behavior easier to inspect.

## Services

- `checkout`: coordinates cart lookup, product pricing, currency conversion,
  payment, shipping, order confirmation, and Kafka post-processing.
- `product-catalog`: serves product list, lookup, and search requests from the
  catalog database with OpenTelemetry SQL instrumentation.
- `frontend`: renders the Astronomy Shop web UI and checkout confirmation flow.
- `cart`: stores user carts in Valkey and exposes cart operations over gRPC.
- `shipping`: returns shipping quotes and tracking IDs through HTTP handlers.

## Code Review Configuration

The branch includes `.coderabbit.yaml` and `REVIEW.md` so CodeRabbit reviews use
assertive review behavior and repository-specific language standards. The
configuration enables incremental review, broad path coverage, strict security
and runtime-safety checks, and code guideline lookup for the language stacks in
this repository.

## Running Locally

Use the existing OpenTelemetry Demo commands from this repository:

```sh
make start
```

After startup, the demo UI and observability tools are available through the
standard local endpoints:

- Webstore: <http://localhost:8080/>
- Jaeger: <http://localhost:8080/jaeger/ui/>
- Grafana: <http://localhost:8080/grafana/>
- Feature Flags UI: <http://localhost:8080/feature/>

## Review Focus

Reviewers should pay close attention to the compatibility transport behavior,
checkout payment diagnostics, retained order state, cart and checkout data flow,
frontend checkout rendering, Kafka post-processing, and the CodeRabbit review
configuration.
