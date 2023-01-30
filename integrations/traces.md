# Traces

When doing RCA in the workbench, one can pick a specific time window when a problem occurred and directly jump to see the traces for that component in that time window. &#x20;

Currently, Trace integration is supported for **Jaeger**

<figure><img src="../.gitbook/assets/Screenshot 2023-01-27 at 3.26.51 PM.png" alt=""><figcaption><p>View the Traces for specific problem</p></figcaption></figure>

### Trace Configuration

**Environment** The environment for which the configuration is applicable

**URL** The base URL of the trace UI

### Jaeger

For Latency Assertions, traces are filtered in Jaeger UI with the duration to focus on problematic traces to find the RCA.

For Error Assertions, if the backend is Elasticsearch then the traces are filtered based on server or client error codes.



**Elasticsearch Backend** Specify whether Jaeger is using Elasticsearch as the trace storage backend.

**Service Label (optional)** If the Jaeger Service name is different from the Entity Name label value then specify the label to be used.

**Operation Label (optional)** If the Jaeger Operation value is different from the Request Context label value, specify the label to be used.

**Label to Tag Mapping (optional)** The correlation labels can be mapped to the tag fields to precisely search traces related to the component, problem, and time window.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2023-01-27 at 6.53.23 PM.png" alt=""><figcaption><p>Jaeger Trace Configuration</p></figcaption></figure>

