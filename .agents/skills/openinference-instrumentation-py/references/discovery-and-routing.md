# Discovery and instrumentor routing

## Safe inspection

Read only the target application's files:

- dependency manifests and lockfiles (`pyproject.toml`, `requirements*.txt`, Poetry/PDM/uv files);
- entrypoints and client construction;
- imports for AI frameworks/provider SDKs;
- existing OpenTelemetry/OpenInference initialization;
- repository-local configuration examples with secret values redacted.

To test configuration presence, inspect a specific name without printing its value, for
example in Python: `os.getenv("NAME") is not None`. Do not dump the process environment.

## Routing order

1. Identify the code path that performs the AI operation.
2. If an agent/framework owns that path, look for its OpenInference instrumentor first.
3. If application code directly calls a provider SDK, look for that SDK's instrumentor.
4. Confirm the package and current API from the matching directory under
   `python/instrumentation/` and its README.
5. If no supported instrumentor exists, say so. Consider manual spans only for a real,
   documented application boundary; do not invent an instrumentor package.

Examples of distinct layers include a framework wrapping a provider SDK. Instrumenting both
can emit duplicate nested LLM spans. Start with the highest applicable layer and inspect the
result before adding another.

## Ambiguities that require confirmation

- multiple services or entrypoints;
- multiple independent AI stacks;
- an existing exporter whose destination is unclear;
- an endpoint variable that may belong to another telemetry pipeline;
- incompatible dependency constraints;
- whether sensitive prompts, outputs, images, or embeddings may be exported.
