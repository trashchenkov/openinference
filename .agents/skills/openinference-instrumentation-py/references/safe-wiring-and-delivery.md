# Safe wiring and delivery

## Existing provider first

Search for `TracerProvider`, `set_tracer_provider`, span processors, exporters, resources,
and framework-specific telemetry setup.

- If no provider exists, create one using the destination's documented setup.
- If an SDK `TracerProvider` already exists, preserve its resource and processors and attach
  only the additional compatible processor/exporter required by the request.
- If only the OpenTelemetry proxy/no-op provider exists, initialize once before clients.
- Preserve unrelated metrics/logging instrumentation.

Call the selected OpenInference instrumentor once, before construction of the client or
framework objects it patches. Pass `tracer_provider=` when the instrumentor API supports it.

## Neutral OTLP

Use the requested OTLP protocol and the official exporter for that protocol. Read endpoint
configuration from documented `OTEL_EXPORTER_OTLP_*` variables or existing app config; do not
hard-code a receiver. For OTLP/HTTP traces, prefer the signal-specific
`OTEL_EXPORTER_OTLP_TRACES_ENDPOINT` when it is present. If only the generic
`OTEL_EXPORTER_OTLP_ENDPOINT` base URL is present and the exporter constructor expects a complete
trace URL, append `/v1/traces` exactly once. Do not pass a generic base endpoint directly as the
exporter `endpoint=` unless the exact exporter API is documented to append the signal path.
Conversely, do not append another signal path to a signal-specific endpoint. Keep protocol and
path semantics exact rather than inferring them from variable names alone.

An exporter configuration is not proof of receipt. Flush short-lived programs when the SDK
requires it and verify at the receiver.

## Existing pipeline

When the task says to preserve an existing pipeline, do not introduce a new backend or alter
its endpoint. Instrument against the current provider and verify that the existing exporter
still receives spans.

## Vendor-specific destinations

Keep neutral OTLP wiring unless the user explicitly requests Phoenix or Arize AX, or the
target application's own configuration conclusively identifies that receiver. For those two
destinations, follow [vendor destination routing](vendor-destinations.md). A generic OTLP
endpoint is not enough evidence to infer either vendor and must not be silently repurposed.

## Privacy

`TraceConfig` supports documented masking controls such as hiding inputs, outputs, messages,
text, images, invocation parameters, tools, prompts, choices, and embedding data. Defaults
favor observability, not privacy. Select controls from the application's policy and verify
actual exported attributes.
