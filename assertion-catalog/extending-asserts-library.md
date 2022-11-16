# Extending Asserts Library

Asserts will continue to grow its support for different application components and frameworks. But there may be cases where some components or frameworks may not yet be supported. Asserts is designed to be very extensible and can be customized to add support for new components and frameworks.

## SAAFE Model

Asserts requires a standard set of metrics to detect resource **S**aturation, **A**nomalies, and changes to the deployment, aka **A**mends, component **F**ailures, and availability/latency **E**rrors. We will illustrate how to map custom metrics to these standard Asserts metrics with some examples. Let's say we have a database **D** and a Prometheus exporter for this database which exports metrics that begin with `d_`.

### Saturation

Suppose the database is deployed using Kubernetes or any container-based orchestration framework. In that case, Asserts will automatically monitor the memory, CPU, disk utilization, and network byte transmission rates based on metrics from cAdvisor/kubelet. &#x20;

#### Query Cache

Suppose the memory used and allocated for query cache are available through custom metrics; we need to define the following recording rule.

```
- record: asserts:resource
  expr: d_query_cache_used_memory_bytes / d_query_cache_allocated_memory_bytes
  labels:
    asserts_entity_type: ServiceInstance
    asserts_resource_type: query_cache
    asserts_source: d_exporter
```

Once this rule is added as described [here](../user-guide/assertion-management.md#howassertsworks-wip-additions), Asserts will automatically start observing for Saturation of the query cache.

#### Client Connections

Suppose there is a limit on the number of active client connections, and there are metrics available to track the current number of active connections and the maximum number of active connections allowed.&#x20;

| Metric                                  | Details                                      |
| --------------------------------------- | -------------------------------------------- |
| `d_active_client_connections_count`     | Number of active client connections          |
| `d_active_client_connections_max_limit` | Maximum number of client connections allowed |

For Asserts to detect whether the client connections are saturating, we will need to define the resource utilization through a recording rule

<pre><code><strong>- record: asserts:resource
</strong>  expr: d_active_client_connections_count / d_active_client_connections_max_limit
  labels:
    asserts_entity_type: ServiceInstance
    asserts_resource_type: d_client_connections
    asserts_source: d_exporter</code></pre>

Asserts will now start observing for Saturation of client connections and raise alerts when the warning or critical threshold is exceeded. If you want to customize the thresholds, you can do it [here.](../user-guide/assertion-management.md#thresholds)

### **Request Rate Anomaly**

Suppose the count of the number of queries is available as a metric broken down by query types. E.g., the number of selects, inserts, updates, and deletes. Asserts can observe the rate at which these queries are being fired and detect anomalies when the query rate deviates from a baseline.&#x20;

#### Mapping Request Counter Metrics

Suppose the request metrics are available as [counters,](https://prometheus.io/docs/concepts/metric\_types/#counter) as shown below.

| Metric                         | Description                                      |
| ------------------------------ | ------------------------------------------------ |
| `d_select_queries_count_total` | The total count of select queries fired so far.  |

```
- record: asserts:request:total
  expr: d_select_queries_count_total
  labels:
    asserts_request_type: query
    asserts_request_context: select                  
```

Similarly, additional recording rules will have to be created for the remaining query types.

Alternatively, the breakup of the counts by query type may also be available as a label in the metric. In such cases, the [label\_replace](https://prometheus.io/docs/prometheus/latest/querying/functions/#label\_replace) function should be used to extract the `asserts_request_context` from an existing label. Also, since the request context is obtained from a label, a single recording rule will suffice for all 4 query types. The example below illustrates this.

| Metric                                 | Description                                    |
| -------------------------------------- | ---------------------------------------------- |
| `d_queries_count_total{type="select"}` | The total count of select queries fired so far |

```
- record: asserts:request:total
  expr: >
    label_replace(
      d_queries_count_total{type!=""}, "asserts_request_context", "$1", "type", "(.*)"
    )
  labels:
    asserts_request_type: query   
```

With the above recording rule, the `asserts:request:total` will now have four metrics with the `asserts_request_context` label now having the respective query type values, i.e., `select`, `insert`, `update` and `delete`

#### Mapping Request Gauge Metrics

Suppose the request metrics are available as [gauges](https://prometheus.io/docs/concepts/metric\_types/#gauge), i.e., the exporter already captures the rate of requests.&#x20;

| Metric                           | Description                           |
| -------------------------------- | ------------------------------------- |
| `d_select_queries_count_per_min` | The rate of select queries per minute |

```
- record: asserts:request:gauge
  expr: d_select_queries_count_per_min/60
  labels:
    asserts_request_type: query
    asserts_request_context: select                    
```

Note that we have divided the metric by `60`. This is because, in Prometheus, the rate is always in units per second.

Asserts will automatically start observing for the anomalies in the rate at which queries are fired. If you want to customize the thresholds, you can do it [here](../user-guide/assertion-management.md#thresholds).

### Errors

Suppose the count of the number of errors is available as a metric. But in addition, the error metric is further broken down by whether it's a system error in executing the query or whether it's due to a user error in the query.  &#x20;

| Metric                              | Description                                                |
| ----------------------------------- | ---------------------------------------------------------- |
| `d_select_query_user_error_total`   | Total count of select query errors due to incorrect query. |
| `d_select_query_system_error_total` | Total count of select query errors due to system issues.   |

#### Mapping Error Counter Metrics

To enable Asserts to observe the errors and raise alerts, the following recording rule will have to be created.&#x20;

```
- record: asserts:error:total
  expr: d_select_query_user_error_total
  labels:
    asserts_error_type: user_error
    asserts_request_type: query
    asserts_request_context: select
 
- record: asserts:error:total
  expr: d_select_query_system_error_total
  labels:
    asserts_error_type: system_error
    asserts_request_type: query
    asserts_request_context: select                         
```

#### Mapping Error Gauge Metrics

The recording rules will be similar to the counter-based rules. Instead of recording `asserts:error:total,` we need to record `asserts:error:gauge`

Asserts will automatically start tracking the error ratio and error budget for each request context and raise the `ErrorRatioBreach` for ratio breaches and `ErrorBuildup` for error budget burn rate breaches.

### Latency

If the latency average and quantile metrics are available, these can also be mapped for Asserts to start observing for anomalies and degradations.&#x20;

#### **Latency Average**

Suppose the latency average metric is directly available, and it is in units of milliseconds; it can be mapped as follows.

```
- record: asserts:latency:average
  expr: d_select_query_latency_millis_avg/1e3
  labels:
    asserts_request_type: query
    asserts_request_context: select 
```

If the latency metric is available as a [Histogram](https://prometheus.io/docs/concepts/metric\_types/#histogram) or [Summary](https://prometheus.io/docs/concepts/metric\_types/#summary), the average can be mapped as follows

```
- record: asserts:latency:total
  expr: d_select_query_latency_millis_sum/1e3
  labels:
    asserts_request_type: query
    asserts_request_context: select
    
- record: asserts:latency:count
  expr: d_select_query_latency_millis_count
  labels:
    asserts_request_type: query
    asserts_request_context: select     
```

#### **Latency P99**

If latency is available as a [Histogram](https://prometheus.io/docs/concepts/metric\_types/#histogram) metric, then the latency P99 can be mapped as

```
- record: asserts:latency:p99
  expr: >
    histogram_quantile (0.99,
      sum(
          rate(d_select_query_latency_millis_bucket[5m])/1e3 > 0
      ) by (le, namespace, workload, job, service, asserts_env, asserts_site)
    )
  labels:
    asserts_request_context: select
    asserts_entity_type: Service
    asserts_request_type: query
```

The above rule records the P99 for the select query at the Service level. For stateless services where there might be a large number of service instances, it's more useful to know the P99 for the entire service. The P99 might have to be computed at the service instance level if the service is stateful.&#x20;

```
- record: asserts:latency:p99
  expr: >
    histogram_quantile (0.99,
      sum(
          rate(d_select_query_latency_millis_bucket[5m])/1e3 > 0
      ) by (le, namespace, workload, job, instance, service, asserts_env, asserts_site)
    )
  labels:
    asserts_request_context: select
    asserts_entity_type: ServiceInstance
    asserts_request_type: query
```

If latency is available as a [Summary](https://prometheus.io/docs/concepts/metric\_types/#summary) metric, then the latency P99 can be mapped as follows.

```
- record: asserts:latency:p99
  expr: >
    d_select_query_latency_millis{quantile="0.99"}/1e3
  labels:
    asserts_request_context: select
    asserts_entity_type: ServiceInstance
    asserts_request_type: query
```

In the case of the summary metric, the p99 is already computed by the exporter, so it cannot be aggregated to the Service level.

### Failure

Suppose our database **D** has a clustered architecture that allows for data to be replicated between multiple replicas, with one replica being the primary and the others being secondaries. Suppose there are metrics to observe the replication health. One could then write an alert rule to report when replication is unhealthy. Problems like these are categorized as `Failure` in Asserts. The existing alert rule can be imported into Asserts by adding additional labels, as explained below.   &#x20;

```
- alert: DClusterReplicationLagHigh
  expr: >
   <QUERY-TO-IDENTIFY-REPLICATION-LAG>
  labels:
    asserts_category: failure
    asserts_entity_type: Service
```

In the above recording rule, we have indicated this is a failure alert by adding the label `asserts_category`. We have also added the `asserts_entity_type` label to indicate that this is a problem at the cluster level and not a specific replica. When this is the case, the metric returned by the query will not have the `instance` label in the result. If the query to identify the replication lag reports the lag for each problematic secondary replica, i.e., the alert query expression returns the `instance` label in the result, then the rule should be written as

```
- alert: DClusterReplicationLagHigh
  expr: >
   <QUERY-TO-IDENTIFY-REPLICATION-LAG-FOR-EACH-AFFECTED-REPLICA>
  labels:
    asserts_category: failure
    asserts_entity_type: ServiceInstance
```
