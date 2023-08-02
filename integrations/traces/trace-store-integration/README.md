# Trace Store Integration

### Trace Integration in Asserts

Asserts integrates with multiple Trace provider backends, that are useful to find out the root cause of the issue. When Assertion fires in Asserts, the user can navigate to the traces. It shows traces in the window of the assertion that occurred with the API/endpoint on which the assertion occurred.

The service name in Asserts is mapped to the service in the trace backed. The request context in Asserts is mapped to `operation/http_route` or `asserts_request_context` depending on how traces are ingested.

The user has the flexibility to map which labels need to be mapped to a Service or operation/http\_route. This is required only if the trace ingestion is not using Asserts OTEL Collector.

### Common Trace Configuration

**Environment** The environment for which the configuration is applicable

**Site** The site for which the configuration is applicable, If not selected applicable for all sites for the environment.

**URL** The base URL of the trace UI.

**Using Asserts OTEL Collector** Specify whether the Asserts OTEL Collector is used to ingest traces to Trace Backend. By default this is ON.

**Add Outbound Request Context in URL** Specify whether the outbound context needs to be added to Trace Integeration URL generation. By default this is ON.

**Service Label (optional)** If the tracing backend's Service name is different from the Entity Name, specify the label to be used.

**Operation Label (optional)** If the tracing backend's operation/http\_route value differs from the Request Context label value, specify the label to use.

**Label to Tag Mapping (optional)** Additional labels that need to be mapped to trace the backend. Specify the labels in asserts to tag in the tracing backend.&#x20;



