# Request, Error and Duration (RED)

### **asserts:request:total**

In Asserts, the `asserts:request:total` records the total count of requests.&#x20;

In **SpringBoot,** the request metrics for incoming requests are available through `http_server_requests_seconds` which is a [Histogram](https://prometheus.io/docs/concepts/metric\_types/#histogram). Similarly the request metrics for outgoing calls are available through `http_client_requests_second` which is also a Histogram. These metrics are mapped to `asserts:request:total` for incoming and outgoing requests.

```
# Incoming requests
- record: asserts:request:total
  expr: http_server_requests_seconds_count
  labels:
    asserts_name: http_server_requests_seconds_count
    asserts_request_type: inbound
    asserts_source: spring_boot

# Outgoing requests made through Spring classes like RestTemplate    
- record: asserts:request:total
  expr: http_client_requests_seconds_count
  labels:
    asserts_name: http_client_requests_seconds_count
    asserts_request_type: outbound
    asserts_source: spring_boot    
```

<table><thead><tr><th width="281">Asserts Meta Label</th><th>Description</th></tr></thead><tbody><tr><td><code>asserts_name</code></td><td>Used by Asserts to keep track of the source metric name from which an Asserts standard metric is recorded.</td></tr><tr><td><code>asserts_request_type</code></td><td>Used by Asserts to categorize requests into different kinds. By default, for all supported http based frameworks, Asserts categorizes requests into <code>inbound</code> for incoming requests and <code>outbound</code> for outgoing http calls. But these can be arbitrary names to group apis e.g. <code>timer_task.</code></td></tr><tr><td><code>asserts_request_context</code></td><td>Used by Asserts to identify a unique request. For http requests, whether <code>inbound</code>, or <code>outbound</code>, this typically maps to the relative part of the request URI with the high cardinality parameters stripped off.  For e.g. <code>/track/order/{}</code> with <code>{}</code> being a placeholder for an order id. Frameworks like Springboot actuator Prometheus metrics  have labels like <code>uri</code>. A <a href="../../user-guide/assertion-management.md#relabelling">relabeling rule</a> is used to map <code>uri</code> to <code>asserts_request_context</code>.</td></tr><tr><td><code>asserts_source</code></td><td>Used by Asserts to identify which framework/instrumentation captured the metric.</td></tr></tbody></table>

In the above Asserts meta labels, except for `asserts_request_context` the remaining labels are specified in the recording rule itself. The `asserts_request_context` is specified through a relabeling rule as follows

```
- source_labels: [asserts_name, uri]
  regex: http_.*_requests_seconds_.*;(.+) # spring boot server and client
  target_label: asserts_request_context
  replacement: $1
```

The label `uri` is available in both the server and client metrics. Notice how the `asserts_name` meta label is being used to target the relabeling only for the metrics of interest. Once these recording rules are [added](https://docs.asserts.ai/user-guide/assertion-management#howassertsworks-wip-additions) to Asserts, the following things will happen&#x20;

* The **Request Rate** will be computed and shown in the Service KPI Dashboard.&#x20;
* The **Request Rate** will be observed for anomalies, and the **RequestRateAnomaly** will be triggered when there are anomalies.

### **asserts:error:total**

In Asserts, the `asserts:error:total` metric records the total count of errors, broken down by different error types. Let's add this rule for SpringBoot `inbound` and `outbound` requests

```
# Inbound request errors
- record: asserts:client:error:total
  expr: http_server_requests_seconds_count{status=~"4.."}
  labels:
    asserts_name: http_server_requests_seconds_count
    asserts_request_type: inbound
    asserts_source: spring_boot

- record: asserts:error:total
  expr: http_server_requests_seconds_count {status=~"5.."}
  labels:
    asserts_name: http_server_requests_seconds_count
    asserts_request_type: inbound
    asserts_error_type: server_errors
    asserts_source: spring_boot

# Outbound request errors
- record: asserts:error:total
  expr: http_client_requests_seconds_count{status=~"4.."}
  labels:
    asserts_name: http_client_requests_seconds_count
    asserts_request_type: outbound
    asserts_error_type: client_errors
    asserts_source: spring_boot

- record: asserts:error:total
  expr: http_client_requests_seconds_count{status=~"5.."}
  labels:
    asserts_name: http_client_requests_seconds_count
    asserts_request_type: outbound
    asserts_error_type: server_errors
    asserts_source: spring_boot    
```

Notice how the `status` label from the Spring metric is used to classify errors into `client_errors` and `server_errors`. Once these rules are added, the following things will happen

* The **Error Ratio** will be computed for all request contexts and shown in the Service KPI Dashboard.&#x20;
* The **ErrorRatioBreach** will be triggered if the ratio breaches a certain threshold.
* The **ErrorBuildup**  (multi burn-multi window) will be triggered if the error budget breaches.
* The **Error Ratio** will be observed for anomalies and **ErrorRatioAnomaly** will be triggered when there are anomalies.

### **asserts:latency:total and asserts:latency:count**

Asserts computes the latency average using the `asserts:latency:total` and `asserts:latency:count` metrics. The former metric is the latency total time in seconds and the latter is the total number of requests. Let's add the recording rules for these two metrics from the respective Histogram metrics which have the `_sum` and `_count`  metrics.

```
# Inbound Latency
- record: asserts:latency:total
  expr: http_server_requests_seconds_sum
  labels:
    asserts_name: http_server_requests_seconds_sum
    asserts_request_type: inbound
    asserts_source: spring_boot

- record: asserts:latency:count
  expr: http_server_requests_seconds_count
  labels:
    asserts_name: http_server_requests_seconds_count
    asserts_request_type: inbound
    asserts_source: spring_boot

# Outbound latency
- record: asserts:latency:total
  expr: http_client_requests_seconds_sum
  labels:
    asserts_name: http_client_requests_seconds_sum
    asserts_request_type: outbound
    asserts_source: spring_boot

- record: asserts:latency:count
  expr: http_client_requests_seconds_count
  labels:
    asserts_name: http_client_requests_seconds_count
    asserts_request_type: outbound
    asserts_source: spring_boot        
```

Once these rules are added. The following things happen

* **Latency Average** is computed for all requests and shown in the Service KPI Dashboards
* The **Latency Average** will be observed for anomalies, and **LatencyAverageAnomaly** will be triggered when there are anomalies.

### **asserts:latency:p99**

Similarly, we can record the latency p99 for the requests as follows

```
# Inbound requests latency P99
- record: asserts:latency:p99
  expr: >
    histogram_quantile (
      0.99,
      sum(rate(http_server_requests_seconds_bucket[5m]) > 0)
        by (le, namespace, job, service, uri, asserts_request_context, asserts_env, asserts_site)
    )
  labels:
    asserts_name: http_server_requests_seconds_bucket
    asserts_entity_type: Service
    asserts_request_type: inbound
    asserts_source: spring_boot

# Outbound requests latency P99
- record: asserts:latency:p99
  expr: >
    histogram_quantile (
      0.99,
      sum(rate(http_client_requests_seconds_bucket[5m]) > 0)
        by (le, namespace, job, service, uri, asserts_request_context, asserts_customer, asserts_env, asserts_site)
    )
  labels:
    asserts_name: http_client_requests_seconds_bucket
    asserts_entity_type: Service
    asserts_request_type: outbound
    asserts_source: spring_boot        
```

Once this is recorded, Asserts will show this metric in the Service KPI Dashboard and also start observing for the clock minutes when the **Latency P99** exceeds a threshold. These minutes will be tracked through a total bad minutes counter. Based on the ratio of `bad minutes` to `total minutes` in a given time-window, the **LatencyP99ErrorBuildup,** a Multi-Burn, Multi-Window error-budget based alert will be triggered.&#x20;

### **asserts:latency:service:p99**

The Latency P99 for the entire service, regardless of different request contexts, can be recorded as follows

```
- record: asserts:latency:service:p99
  expr: >
    histogram_quantile (
      0.99,
      sum(rate(http_server_requests_seconds_bucket[5m]) > 0)
        by (le, namespace, job, service, asserts_env, asserts_site)
    )
  labels:
    asserts_name: http_server_requests_seconds_bucket
    asserts_entity_type: Service
    asserts_request_type: inbound
    asserts_source: spring_boot
```

This metric is useful while creating a Latency SLO for the entire service.
