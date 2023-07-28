# Trace Storage

Asserts does not have a proprietary trace store as there are plenty of trace store options available. Asserts can integrate with Grafana Tempo, Google CloudTrace, AWS X-Ray, Jaeger or any other trace store that either offers an OTLP compatible ingestion API or has an [OpenTelemetry exporter](https://opentelemetry.io/docs/concepts/components/#exporters) component for a proprietary API.&#x20;

While ingesting traces to the tracing backend, the traces from language-specific agents can be sent to the [Asserts OTEL collector ](asserts-otel-collector/)and the asserts OTEL collector be configured to ingest the traces to the tracing backend.

The [Asserts OTEL Collector](asserts-otel-collector/) has the intelligence to find the error, slow and normal traces. It is recommended that the [Asserts OTel Collector](asserts-otel-collector/) be used for trace ingestion as it will significantly reduce your trace volumes without loosing the insights needed when troubleshooting problems.
