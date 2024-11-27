# Request, Error and Duration (RED)

**NOTE:** These instructions are meant for a quick PoC to demonstrate capability. They involve creating copy of existing metrics and hence would lead to additional metrics being created. In customer environments, this might be acceptable only if the scope of the data being looked at is small.

### **asserts:request:total**

In Asserts, the `asserts:request:total` records the total count of requests.&#x20;

In **SpringBoot,** the request metrics for incoming requests are available through `http_server_requests_seconds` which
is a [Histogram](https://prometheus.io/docs/concepts/metric\_types/#histogram). Similarly, the request metrics for
outgoing calls are available through `http_client_requests_second` which is also a Histogram. These metrics are mapped
to `asserts:request:total` for incoming and outgoing requests.

```
# Incoming requests
- record: asserts:request:total
  expr: |- 
    label_replace(http_server_requests_seconds_count, "asserts_request_context", "$1", "uri", "(.+)")
  labels:
    asserts_source: spring_boot  
    asserts_metric_request: total  
    asserts_request_type: inbound

# Outgoing requests made through Spring classes like RestTemplate    
- record: asserts:request:total
  expr: |-
    label_replace(http_client_requests_seconds_count, "asserts_request_context", "$1", "uri", "(.+)")
  labels:
    asserts_source: spring_boot  
    asserts_metric_request: total  
    asserts_request_type: outbound        
```

<table>
  <thead><tr><th width="281">Asserts Meta Label</th><th>Description</th></tr></thead>
  <tbody>
    <tr><td><code>asserts_source</code></td><td>Used by Asserts to identify which framework/instrumentation captured the metric.</td></tr>
    <tr><td><code>asserts_metric_request</code></td><td>Used by Asserts to identify this as a request metric. Valid values are <code>total</code> when source metric is a counter and <code>gauge</code> when source metric is a gauge</td></tr>
    <tr><td><code>asserts_request_type</code></td><td>Used by Asserts to categorize requests into different kinds. By default, for all supported http based frameworks, Asserts categorizes requests into <code>inbound</code> for incoming requests and <code>outbound</code> for outgoing http calls. But these can be arbitrary names to group apis e.g. <code>timer_task</code> or <code>query</code> etc</td></tr>
    <tr><td><code>asserts_request_context</code></td><td>Used by Asserts to identify a unique request. For http requests, whether <code>inbound</code>, or <code>outbound</code>, this typically maps to the relative part of the request URI with the high cardinality parameters stripped off.  For e.g. <code>/track/order/{}</code> with <code>{}</code> being a placeholder for an order id. Frameworks like Springboot actuator Prometheus metrics  have labels like <code>uri</code>. The <a href="https://prometheus.io/docs/prometheus/latest/querying/functions/#label_replace">label_replace</a> function is used to map <code>uri</code> to <code>asserts_request_context</code>.</td></tr>
  </tbody>
</table>

Once these rules are added, the following things will happen

* The **Request Rate** will be computed and shown in the Service KPI Dashboard.&#x20;
* The **Request Rate** will be observed for anomalies, and the **RequestRateAnomaly** will be triggered when there are
  anomalies.

<b>NOTE:</b> In the above example, the source metric is available as a counter. So it was mapped to <code>asserts:request:total</code>. If the source metric were a gauge,
then it should be mapped to <code>asserts:request:gauge</code> and set <code>asserts_metric_request: gauge</code>.

### **asserts:error:total**

In Asserts, the `asserts:error:total` metric records the total count of errors, broken down by different error types.
Let's add this rule for SpringBoot `inbound` and `outbound` requests

```
# Inbound request errors
- record: asserts:client:error:total
  expr: |
    label_replace(http_server_requests_seconds_count{status=~"4.."}, "asserts_request_context", "$1", "uri", "(.+)")
  labels:
    asserts_source: spring_boot
    asserts_metric_error: client_total    
    asserts_request_type: inbound

- record: asserts:error:total
  expr: |
    label_replace(http_server_requests_seconds_count {status=~"5.."}, "asserts_request_context", "$1", "uri", "(.+)")
  labels:
    asserts_source: spring_boot  
    asserts_metric_error: total    
    asserts_request_type: inbound
    asserts_error_type: server_errors

# Outbound request errors
- record: asserts:error:total
  expr: |
    label_replace(http_client_requests_seconds_count{status=~"4.."}, "asserts_request_context", "$1", "uri", "(.+)")
  labels:
    asserts_source: spring_boot  
    asserts_metric_error: total    
    asserts_request_type: outbound
    asserts_error_type: client_errors

- record: asserts:error:total
  expr: |
    label_replace(http_client_requests_seconds_count{status=~"5.."}, "asserts_request_context", "$1", "uri", "(.+)")
  labels:
    asserts_source: spring_boot  
    asserts_metric_error: total     
    asserts_request_type: outbound
    asserts_error_type: server_errors
```

<table>
  <thead><tr><th width="281">Asserts Meta Label</th><th>Description</th></tr></thead>
  <tbody>
    <tr><td><code>asserts_metric_error</code></td><td>Used by Asserts to identify this as an error metric of type counter. Valid values are <code>client_total</code> and <code>client_gauge</code></td></tr>
    <tr><td><code>asserts_error_type</code></td><td>Used by Asserts to categorize errors into different kinds. The commonly useful types are <code>server_errors</code> and <code>client_errors</code>. In this example, a condition on the <code>status</code> label has been used to define these types. Note that the client errors for <code>inbound</code> calls are mapped using a special type <code>client_total</code>. This is because, the inbound client errors tend to be noisy. Asserts will still observe them, but the signals captured surface only when there are anomalies. I.E. if there is a steady stream of client errors, there won't be a signal. However, if there is a sudden change in the rate of these errors, then an anomaly signal will be generated.</td></tr>
  </tbody>
</table>

Once these rules are added, the following things will happen

* The **Error Ratio** will be computed for all request contexts and shown in the Service KPI Dashboard.&#x20;
* The **ErrorRatioBreach** will be triggered if the ratio breaches a certain threshold.
* The **ErrorBuildup**  (multi burn-multi window) will be triggered if the error budget breaches.
* The **Error Ratio** will be observed for anomalies and **ErrorRatioAnomaly** will be triggered when there are
  anomalies.

<b>NOTE:</b> In the above example, the source metric is available as a counter. So it was mapped to <code>asserts:error:total</code>. If the source metric were a gauge,
then it should be mapped to <code>asserts:error:gauge</code> and set <code>asserts_metric_error: gauge</code> or <code>asserts_metric_error: client_gauge</code> in the case of inbound client errors.

### **asserts:latency:total and asserts:latency:count**

Asserts computes the latency average using the `asserts:latency:total` and `asserts:latency:count` metrics. The former
metric is the latency total time in seconds and the latter is the total number of requests. Let's add the recording
rules for these two metrics from the respective Histogram metrics which have the `_sum` and `_count`  metrics.

```
# Inbound Latency
- record: asserts:latency:total
  expr: |
    label_replace(http_server_requests_seconds_sum, "asserts_request_context", "$1", "uri", "(.+)")
  labels:
    asserts_source: spring_boot  
    asserts_metric_latency: seconds_sum    
    asserts_request_type: inbound

- record: asserts:latency:count
  expr: |
    label_replace(http_server_requests_seconds_count, "asserts_request_context", "$1", "uri", "(.+)")
  labels:
    asserts_source: spring_boot  
    asserts_metric_latency: count    
    asserts_request_type: inbound

# Outbound latency
- record: asserts:latency:total
  expr: 
    label_replace(http_client_requests_seconds_sum, "asserts_request_context", "$1", "uri", "(.+)")
  labels:
    asserts_source: spring_boot  
    asserts_metric_latency: seconds_sum    
    asserts_request_type: outbound

- record: asserts:latency:count
  expr: 
    label_replace(http_client_requests_seconds_count, "asserts_request_context", "$1", "uri", "(.+)")
  labels:
    asserts_source: spring_boot 
    asserts_metric_latency: count     
    asserts_request_type: outbound
```

<table>
  <thead><tr><th width="281">Asserts Meta Label</th><th>Description</th></tr></thead>
  <tbody>
    <tr><td><code>asserts_metric_latency</code></td><td>Used by Asserts to identify the numerator and denominator to compute the latency average along with the unit of the source latency metric. Valid values for latency, the numerator, are <code>seconds_sum</code>, <code>milliseconds_sum</code> and <code>microseconds_sum</code>. For the latency count, the denominator, the valid value is <code>count</code></td></tr>
  </tbody>
</table>

Once these rules are added. The following things happen

* **Latency Average** is computed for all requests and shown in the Service KPI Dashboards
* The **Latency Average** will be observed for anomalies, and **LatencyAverageAnomaly** will be triggered when there are
  anomalies.

<b>NOTE:</b> In the above example, the source metric is available as a counter. So it was mapped to <code>asserts:latency:total</code> and <code>asserts:latency:count</code>. If the source metric were a gauge,
then it should be directly mapped to <code>asserts:latency:average</code>. While doing this, be mindful of the labels in the source metric.
When the source is a counter, Asserts does some aggregation internally and only the key labels are retained reducing the cardinality in the metrics it records. In the direct mapping this is not the case.


### **asserts:latency:p99**

Similarly, we can record the latency p99 for the requests as follows

```
# Inbound requests latency P99
- record: asserts:latency:p99
  expr: >
    label_replace(
      histogram_quantile (
        0.99,
        sum(rate(http_server_requests_seconds_bucket[5m]) > 0) by (le, namespace, job, service, workload, uri, asserts_env, asserts_site)
      )
      , "asserts_request_context", "$1", "uri", "(.+)"
    )
  labels:
    asserts_source: spring_boot  
    asserts_entity_type: Service
    asserts_request_type: inbound

# Outbound requests latency P99
- record: asserts:latency:p99
  expr: >
    label_replace(  
      histogram_quantile (
        0.99,
        sum(rate(http_client_requests_seconds_bucket[5m]) > 0) by (le, namespace, job, service, workload, uri, asserts_env, asserts_site)
      )
      , "asserts_request_context", "$1", "uri", "(.+)"
    )
  labels:
    asserts_source: spring_boot  
    asserts_entity_type: Service
    asserts_request_type: outbound
```

<table>
  <thead><tr><th width="281">Asserts Meta Label</th><th>Description</th></tr></thead>
  <tbody>
    <tr><td><code>asserts_env</code></td><td>Used by Asserts to identify the environment. All discovered entities and observed metrics are automatically scoped to an environment.</td></tr>
    <tr><td><code>asserts_site</code></td><td>Used by Asserts to identify the region/site within an environment. For e.g. you could have a <code>prod</code> environment but multiple regions, for e.g. <code>us-east-1, us-west-2 etc</code>. This label is then used to capture the region information. Note that this depends on how environment information is encoded in the metrics. Sometimes, both the environment and the region information may be encoded in a single label value, in which case, the <code>asserts_env</code> will have that value and this label may not be present.</td></tr>
    <tr><td><code>asserts_entity_type</code></td><td>Used by Asserts to identify at what level is the metric being observed. The <code>workload</code>, <code>service</code>, <code>job</code> are special labels that Asserts uses to identify the <code>Service</code>. These are the same labels that are also used to discover the <code>Service</code> entity in the Asserts entity model. In this example, while aggregating, these are the labels being retained. So this metric will be observed for the corresponding <code>Service</code> entity</td></tr>
  </tbody>
</table>

Once this is recorded, Asserts will show this metric in the Service KPI Dashboard and also start observing for the clock
minutes when the **Latency P99** exceeds a threshold. These minutes will be tracked through a total bad minutes counter.
Based on the ratio of `bad minutes` to `total minutes` in a given time-window, the **LatencyP99ErrorBuildup,** a
Multi-Burn, Multi-Window error-budget based alert will be triggered.&#x20;

### **asserts:latency:service:p99**

The Latency P99 for the entire service, regardless of different request contexts, can be recorded as follows

```
- record: asserts:latency:service:p99
  expr: >
    histogram_quantile (
      0.99,
      sum(rate(http_server_requests_seconds_bucket[5m]) > 0)
        by (le, namespace, job, service, workload, asserts_env, asserts_site)
    )
  labels:
    asserts_entity_type: Service
    asserts_request_type: inbound
    asserts_source: spring_boot
```

This metric is useful while creating a Latency SLO for the entire service.
