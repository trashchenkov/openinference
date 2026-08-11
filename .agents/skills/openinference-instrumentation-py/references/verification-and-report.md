# Verification and instrumentation report

## Verification ladder

1. Manifest parses and dependency resolution is coherent.
2. Imports succeed in the application's own environment.
3. Existing application behavior still passes its tests or deterministic smoke.
4. The instrumented path executes against an approved real, local, or mock dependency.
5. A receiver/exporter observes spans.
6. Inspect span count, parent-child structure, OpenInference span kind, input/output policy,
   error/status behavior, and absence of duplicate LLM spans.
7. Confirm the requested destination received the trace when credentials/network are approved.

Do not collapse these into one claim. A successful import is not an emitted span; an emitted
span is not confirmed backend delivery.

## Blockers

Use precise blockers such as: package resolution unavailable, unsupported SDK version, no
instrumentor exists, credentials absent, network denied, receiver unavailable, app cannot
reach its model dependency, or existing provider type cannot accept a processor. Never bypass
a blocker with invented output.

## `instrumentation-report.json`

When the task requests it, write exactly the schema supplied by the task. For the benchmark
contract this is:

```json
{
  "instrumented": false,
  "instrumentor_packages": [],
  "no_instrumentor_available": false,
  "incompatibility_detected": null,
  "nothing_to_instrument": false,
  "manual_spans_added": false,
  "delivery_target": "none",
  "notes": ""
}
```

Replace example literals with facts. `delivery_target` must match the task's allowed enum and
actual configured result. Set `instrumented` only when instrumentation was genuinely added or
already present and validated. Report unsupported/incompatible/nothing-to-instrument cases
rather than creating synthetic traces.

Keep notes concise and avoid secrets, raw prompt/output content, or unsupported claims.
