# Google CloudTrace

For Latency Assertions, traces are filtered in Cloud Trace UI with the duration to focus on problematic Traces to find the RCA if Asserts OTEL Collector is not used.

For Error Assertions, the traces are filtered based on server or client error codes.

**Project** Specify the Cloud Trace project in which the traces are ingested.

**Using OTEL Agent to Ingest Traces Directly (optional)** Specify whether OTEL Agent is used to ingesting the trace directly to Trace backend.

<figure><img src="../../../.gitbook/assets/google-cloud-trace-configuration.png" alt=""><figcaption><p>Google Cloud Trace Configuration</p></figcaption></figure>
