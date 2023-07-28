# Traces

When doing RCA in the workbench, one can pick a specific time window when a problem occurred and directly jump to see the traces for that component in that time window. &#x20;

Currently, Trace integration is supported for Jaeger, Google Cloud Trace, AWS XRay, and Grafana Tempo

<figure><img src="../../.gitbook/assets/Screenshot 2023-01-27 at 3.26.51 PM.png" alt=""><figcaption><p>View the Traces for specific problem</p></figcaption></figure>

### Trace Integration in Asserts

Asserts integrates with multiple Trace provider backends, that are useful to find out the root cause of the issue. When Assertion fires in Asserts, the user can navigate to the traces. It shows traces in the window of the assertion that occurred with the API/endpoint on which the assertion occurred.

The service name in Asserts is mapped to the service in the trace backed. The request context in Asserts is mapped to operation/http\_route or asserts\_request\_context depending on how traces are ingested.

The user has the flexibility to map which labels need to be mapped to a Service or operation/http\_route. This is required only if the trace ingestion is not using Asserts OTEL Collector.

### Ingesting Traces

While ingesting traces to the tracing backend, the user adopts any of the following methods

* Ingest the traces directly to the tracing backend using the language-specific agents.
* Send the traces from language-specific agents to a community collector and the collector ingest the traces to the tracing backend.
* Send the traces from language-specific agents to an **Asserts OTEL collector** and the asserts OTEL collector ingests the traces to the tracing backend.

The **Asserts OTEL Collector** has the intelligence to find the error, slow and normal traces. Also, it will send the traces to the backend that are relevant for RCA.

### Common Trace Configuration

**Environment** The environment for which the configuration is applicable

**Site** The site for which the configuration is applicable, If not selected applicable for all sites for the environment.

**URL** The base URL of the trace UI.

**Using Asserts OTEL Collector** Specify whether the Asserts OTEL Collector is used to ingest traces to Trace Backend. By default this is ON.

**Add Outbound Request Context in URL** Specify whether the outbound context needs to be added to Trace Integeration URL generation. By default this is ON.

**Service Label (optional)** If the tracing backend's Service name is different from the Entity Name, specify the label to be used.

**Operation Label (optional)** If the tracing backend's operation/http\_route value differs from the Request Context label value, specify the label to use.

**Label to Tag Mapping (optional)** Additional labels that need to be mapped to trace the backend. Specify the labels in asserts to tag in the tracing backend.&#x20;

###

### Google Cloud Trace

For Latency Assertions, traces are filtered in Cloud Trace UI with the duration to focus on problematic Traces to find the RCA if Asserts OTEL Collector is not used.

For Error Assertions, the traces are filtered based on server or client error codes.

**Project** Specify the Cloud Trace project in which the traces are ingested.

**Using OTEL Agent to Ingest Traces Directly (optional)** Specify whether OTEL Agent is used to ingesting the trace directly to Trace backend.

<figure><img src="../../.gitbook/assets/Screenshot 2023-05-17 at 2.22.20 PM.png" alt=""><figcaption><p>Google Cloud Trace Configuration</p></figcaption></figure>

### AWS XRay

For Latency Assertions, traces are filtered in AWS XRay URL with the duration to focus on problematic Traces to find the RCA if Asserts OTEL Collector is not used.

For Error Assertions, the traces are filtered based on server or client error codes.



**AWS Region** Specify the Region traces ingested.

<figure><img src="../../.gitbook/assets/Screenshot 2023-05-17 at 2.26.58 PM.png" alt=""><figcaption><p>AWS XRay Configuration</p></figcaption></figure>

### Grafana Tempo

For Latency Assertions, traces are filtered in Grafana Tempo URL with the duration to focus on problematic Traces to find the RCA if Asserts OTEL Collector is not used.

For Error Assertions, the traces are filtered based on server or client error codes.



**Org Id** Specify the Org Id configured in Grafana for Tempo.

**Data Source** Specify the Data Source configured in Grafana for Tempo.

<figure><img src="../../.gitbook/assets/Screenshot 2023-05-17 at 3.52.49 PM.png" alt=""><figcaption><p>Grafana Tempo Configuration to a Customer Tempo Service</p></figcaption></figure>
