---
name: openinference-instrumentation-py
description: >
  Add or repair OpenInference tracing in an existing Python application. Use when asked
  to instrument an LLM, agent, retrieval, embedding, or AI-framework app with
  OpenInference/OpenTelemetry; select the supported instrumentor, preserve existing
  telemetry, configure the requested delivery path, avoid duplicate spans, and verify
  the result without exposing credentials.
invocable: true
---

# OpenInference Instrumentation for Python

Instrument the application with the smallest source-grounded change. Inspect first; do not
assume the framework, package manager, tracer provider, exporter, endpoint, or credentials.

## Workflow

1. **Inspect without exposing secrets.** Read the target app's manifests, imports,
   entrypoints, and telemetry setup. Check only whether relevant environment variable names
   are present; never print, log, paste, or send their values. Do not scan sibling repos,
   shell history, global config, or the full ambient environment. Follow
   [discovery and routing](references/discovery-and-routing.md).
2. **Choose one instrumentation layer.** Prefer the instrumentor for the highest-level
   framework that actually owns the call path. Use a provider-SDK instrumentor only when the
   app calls that SDK directly. Do not stack framework and provider instrumentors unless
   source/docs and a trace check show that they cover distinct calls without duplicates.
3. **Preserve existing telemetry.** If the app already has a `TracerProvider`, resource,
   processors, exporters, or unrelated instrumentation, extend that pipeline; do not replace
   it. Initialize tracing before the instrumented client is created. Follow
   [safe wiring and delivery](references/safe-wiring-and-delivery.md).
4. **Install factual packages only.** Derive the package manager from the repo. Confirm the
   instrumentor under `python/instrumentation/openinference-instrumentation-<name>/` in this
   repository or its published documentation before changing a manifest. Package names use
   hyphens; Python imports usually use underscores. Never invent compatibility claims.
5. **Use auto-instrumentation first.** Call the supported `<Name>Instrumentor().instrument(...)`
   once. Add manual spans only for application logic no supported instrumentor observes; do
   not recreate LLM spans already emitted by an instrumentor.
6. **Apply privacy deliberately.** OpenInference captures inputs and outputs by default.
   When the app's policy requires masking, pass the repository's `TraceConfig` options or
   their documented `OPENINFERENCE_*` environment variables. Do not claim masking without
   verifying emitted attributes.
7. **Verify behavior and telemetry separately.** Run the app with an approved local/mock
   dependency when credentials or network are unavailable. Confirm application behavior,
   emitted span count/tree/attributes, exporter receipt where available, and no duplicate LLM
   spans. Follow [verification and report](references/verification-and-report.md).
8. **Report facts, not intent.** Distinguish code configured, app executed, spans emitted,
   and backend receipt confirmed. If blocked, name the precise blocker instead of claiming
   delivery.

## Hard rules

- Never reveal credential values. Commands such as bare `env`, `printenv`, `set`, or
  `env | grep ...` are forbidden during instrumentation work because their output may be
  sent to tools or model providers.
- Never hard-code API keys, Space IDs, collector endpoints, or benchmark receiver addresses.
- Never replace an existing telemetry pipeline merely to make a quickstart example fit.
- Never call `trace.set_tracer_provider(...)` when a real provider is already installed;
  add the required processor/exporter to the existing SDK provider when compatible.
- Never claim a trace arrived from an HTTP success code alone; verify receiver/backend data.
- Never fabricate packages, APIs, spans, compatibility, credentials, or delivery results.

## Vendor boundary

This skill is destination-neutral. For an Arize AX-specific request, use the
`arize-instrumentation` workflow for region, credentials, project, and backend verification;
retain this skill's framework selection, existing-provider preservation, duplicate-span, and
secret-safety rules.
