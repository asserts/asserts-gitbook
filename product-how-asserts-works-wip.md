# How Asserts Processes Data

As an Asserts customer, all you need to do is to forward your Prometheus metrics to Asserts. This can be done with a simple remote write config. Once the metrics are in Asserts land, a few things happen.

### Discovery <a id="HowAssertsWorks(WIP)-Discovery"></a>

First, we inspect their labels to discover various entities and populate their properties. In addition, we deduce the relationships between them by matching their properties or matching against certain metrics that directly establish relations. As a result, we can determine which pod is hosted on which node, which pods form a service, and how services call each other.

All these entities, properties, and relationships form a knowledge graph that describes our understanding of the system. They are also indexed to be easily searchable. The discovery process constantly updates the graph while at the same time keeps the history.

![](.gitbook/assets/image%20%281%29.png)

### Normalization <a id="HowAssertsWorks(WIP)-Normalization"></a>

Secondly, Asserts has curated a set of rules to normalize the incoming heterogeneous time series into a set of essential metrics, like RED metrics \(Request, Error, Duration\) for application components, and utilization metrics for infrastructure components.

For example, the RED metrics from Springboot will be recorded as Prometheus counter `asserts:request:total`, `asserts:latency:total`, and `asserts:error:total`

```yaml
- record: asserts:request:total
  expr: http_server_requests_seconds_count
  labels:
    asserts_request_type: inbound
    asserts_source: spring_boot

- record: asserts:latency:total
  expr: http_server_requests_seconds_sum
  labels:
    asserts_request_type: inbound
    asserts_source: spring_boot

- record: asserts:error:total
  expr: http_client_requests_seconds_count{status=~"5.."}
  labels:
    asserts_request_type: outbound
    asserts_error_type: server_errors
    asserts_source: spring_boot
```

We add labels like `asserts_request_type`, `asserts_error_type`, etc., to indicate the level of granularity for further processing in instrumentation. Some more dynamic context information like HTTP paths will be recorded in `asserts_request_context` with Prometheus relabelling rule at ingestion time.

If a customer has metrics from multiple environments \(dev, stage, prod\), or multiple sites \(region, data center, etc\), he/she can use a combination of external labels and relabeling rules to add `asserts_env` and `asserts_site` labels to scope metrics and thus entities discovered from them. Asserts provides `env` and `site` dropdown filters in the Web App to segment the graph into different environments and sites, while at the same time still keep everything in a single graph to facilitate cross-environment/site correlation or comparison.

### Assertion <a id="HowAssertsWorks(WIP)-Assertion"></a>

We then apply our extensive domain knowledge to instrument these normalized metrics. Out-of-box we automatically instrument application frameworks like Springboot, Flask, Loopback, etc, infrastructure components like Kubernetes resources, 3rd party services like Redis server, Kafka cluster, and many more.

With instrumentation in place, we form a SAAFE model to capture events as what we call “_assertions_”

* **S**aturation indicates whether a resource \(CPU, Memory, etc\) is saturated
* **A**mend captures changes in the system, like deployment, scaling, config map change, etc.
* **A**nomaly captures abnormal shifts in request rate, latency, or resource consumption
* **F**ailure records failure state in the system, like primary-standby sync failure, pod crash looping, etc.
* **E**rror records problematic requests, i.e, 500x, 400x, or breaches of latency thresholds, etc.

Assertions thus become condensed time-series data that only capture important events in the system. They bear Assert’s deep knowledge on the observability of various building blocks in a modern application.

Assertions are different from traditional alerts, as they are not meant to be used to notify on-call personnel. They are more like vital signs surfaced automatically by Asserts, and ready to be used in troubleshooting. Sure enough, customers can choose to subscribe to selected assertions and fulfill their role as traditional alerts.

### Correlation <a id="HowAssertsWorks(WIP)-Correlation"></a>

Asserts’s story doesn’t stop with automatic instrumentation. Once assertions arise, we do a few more things

* we attach them back to the graph. This, when combined with indexing, provides a powerful way to navigate both entities and their health status in the graph
* we enrich the assertions with context information from the graph. For example, an assertion that happened on a pod can be tagged back to the node and service the pod belongs to. This allows assertions that happened on ephemeral entities \(pods\) to bubble up to long-lived entities \(nodes, services\), thus forming an aggregated view with a continuous timeline.

Since assertions are condensed and contextualized, they are much faster to query and aggregate, much easier to correlate or rank, thus enabling quick and precise root cause analysis.



