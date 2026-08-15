# Vendor destination routing

Read this reference only when the user explicitly requests Phoenix or Arize AX, or the target
application's own configuration conclusively identifies one of them. The core skill remains
authoritative for instrumentor selection, initialization order, existing-provider preservation,
duplicate-span prevention, privacy, and evidence-based reporting.

## Phoenix

Use the official
[`phoenix-tracing`](https://github.com/Arize-ai/phoenix/tree/main/.agents/skills/phoenix-tracing)
skill for Phoenix-specific installation, server/exporter configuration, projects, sessions,
production batching and masking, and backend inspection. Do not assume that the companion skill
is installed: if it is unavailable, consult the linked official source or current
[Phoenix documentation](https://docs.arize.com/phoenix) rather than reconstructing its setup
from memory.

Do not assume a local or hosted Phoenix endpoint, authentication mode, or project. Preserve an
existing OpenTelemetry provider and unrelated exporters. Confirm receipt from Phoenix data, not
only from exporter configuration or an HTTP success response.

## Arize AX

Use the official
[`arize-instrumentation`](https://github.com/Arize-ai/arize-skills/tree/main/skills/arize-instrumentation)
skill for AX-specific integration routing, region and endpoint selection, credentials, Space and
project configuration, and backend verification. Do not assume a region, endpoint, project, API
key, or Space ID, and never display credential values.

A generic `OTEL_EXPORTER_OTLP_ENDPOINT` may belong to a non-Arize pipeline. Preserve that pipeline
unless the target application's own configuration or the user confirms that it points to AX.
Confirm receipt using the official AX verification workflow; configuration alone is not proof
that a trace arrived.

## Reporting

For either destination, report portable instrumentation and vendor delivery separately:

- instrumentor selected and initialized;
- application path executed;
- spans emitted locally;
- exporter configured;
- destination receipt confirmed, or the exact verification blocker.

Never collapse these milestones into a single claim that tracing "works."