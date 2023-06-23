# Resource Utilization and Saturation

### **asserts:resource**

Some resources have a finite limit, and it is possible to express their current level of utilization as a ratio against the limit, and observe their utilization and get early warning of their saturation. For such cases, we need to record the `asserts:resource` metric.&#x20;

Let's take the example of the number of clients for a Redis server. Redis has a config to limit the max number of clients. This limit as well as the current number of clients is available through metrics exposed by the redis exporter.&#x20;

<table><thead><tr><th width="418">Metric</th><th>Details</th></tr></thead><tbody><tr><td><code>redis_connected_clients</code></td><td>Number of active client connections</td></tr><tr><td><code>redis_config_maxclients</code></td><td>Maximum number of client connections allowed</td></tr></tbody></table>

If we think of the number of clients as a finite resource, it's useful to track it's utilization and get early warning before it actually saturates. To achieve these objectives, let's record the `asserts:resource` metric &#x20;

```
- record: asserts:resource
  expr: >
    max by (asserts_env, asserts_site, namespace, service, job) (
      redis_connected_clients / redis_config_maxclients
    )
  labels:
    asserts_entity_type: Service
    asserts_resource_type: client_connections
    asserts_source: redis_exporter
```

#### Understanding the Asserts meta labels and aggregation

**asserts\_env** and **asserts\_site**

The same redis service may be deployed in multiple environments. In Asserts, all services and other infrastructure components are grouped by environment and site. For e.g. `asserts_env = prod` and `asserts_site = us-west-2`.  The metrics, alerts and the services discovered from these metrics are scoped by these labels.

**namespace and service**&#x20;

There may be multiple deployments of redis for different functionalities. For e.g. `redis-payments`, `redis-orders` etc. In K8s, these would be deployed as different stateful services. Asserts uses the `namespace` and `service` label in the metric to uniquely identify each service in a given environment.

**job**

In non-k8s environment, the prometheus metric scrape configuration will have a different value for the `job` label for each different deployment of redis. Asserts uses the `job` label in the metric to uniquely identify each service in a given environment.

**Aggregation**

So far we have identified the labels that will uniquely identify the service in a multi-environment, multi-service setup. But redis may also be setup as a cluster with multiple instances. Since the connection count metric is available at the instance level, the utilization is computed at the instance level. But we want the alert to fire for the redis service and not for a specific instance. So we track the highest utilization amongst all instances by using the `max by(...)` aggregation. We retain the `asserts_env, asserts_site, namespace, service, job` labels in the aggregation clause to help Asserts associate the alert with the correct service entity.&#x20;

**asserts\_entity\_type**

In Asserts entity model, all infrastructure components are modeled as different types of entities. For e.g. **Service, ServiceInstance, Pod, Node** etc. Redis is a **Service** and the different instances of a clustered redis are **ServiceInstance**s**.**  But as explained in the `Aggregation` section earlier, we want Asserts to associate the alert at the service level. This is achieved by the meta label specification `asserts_entity_type: Service` .&#x20;

If we want to track the connection saturation at an individual instance the rule can be tweaked to

```
- record: asserts:resource
  expr: >
    max by (asserts_env, asserts_site, namespace, service, job, instance) (
      redis_connected_clients / redis_config_maxclients
    )
  labels:
    asserts_entity_type: ServiceInstance
    asserts_resource_type: client_connections
    asserts_source: redis_exporter
```

The expr now includes the `instance` label and entity\_type is set to `ServiceInstance`.

**asserts\_resource\_type**&#x20;

Similarly, Asserts models the various resources that it observes into different types. For e.g. `cpu:usage` , `memory:usage`, `disk:usage` etc are different types of resources. In this example, what is being observed is `client_connections`. You may set it to any value that best describes the resource and signal being observed. &#x20;

**asserts\_source**

Sometimes, the same or similar metrics may be available from multiple instrumentations. The `asserts_source` is a useful meta label to indicate what is the source exporter of the metric. This is helpful when investigating the alert.

We now have a recording rule to track utilization. We also understand the different parts of the recording rule. Asserts will now start observing for utilization of client connections and raise alerts when the warning or critical threshold is exceeded. Asserts has a default threshold for all resource utilization. The thresholds for Saturation of different resources can be configured [here.](../../user-guide/assertion-management.md#thresholds)

### **asserts:resource:total**

In some cases, there may be value in observing anomalous changes in a resource metric. These resource metrics are available as [gauge](https://prometheus.io/docs/concepts/metric\_types/#gauge)s or [counters](https://prometheus.io/docs/concepts/metric\_types/#counter). For e.g., the total number of network bytes received or transmitted is monotonically increasing with time and is available as counters. An unusual spike in the bytes received or transmitted is an anomaly. To observe such anomalies, we need to record the `asserts:resource:total` metric. Let's do this for the network\_bytes.

```
# For anomaly detection on in-bytes
- record: asserts:resource:total
  expr: redis_net_input_bytes_total
  labels:
    asserts_entity_type: ServiceInstance
    asserts_resource_type: network:rx_bytes
    asserts_source: redis_exporter

# For anomaly detection on out-bytes
- record: asserts:resource:total
  expr: redis_net_output_bytes_total
  labels:
    asserts_entity_type: ServiceInstance
    asserts_resource_type: network:tx_bytes
    asserts_source: redis_exporter
```

Notice that in the above rule, the `asserts_entity_type` is set to `ServiceInstance`. This is because we want to observe network transmission anomaly at the specific redis instance level. This will also ensure that the network bytes transfer rate is shown in the Service KPI Dashboard.

### **asserts:resource:gauge**

When resource metrics are available as [gauge](https://prometheus.io/docs/concepts/metric\_types/#gauge)s by mapping them to  `asserts:resource:gauge` Asserts will automatically detect anomalies. For e.g., while we are observing the utilization and saturation of client connections, it might also be interesting to observe for anomalies in the number of connections. For e.g. a sudden drop or spike in the number of client connections is an anomaly. To observe such anomalies, the number of client connections can be recorded as resource gauge as follows -

```
- record: asserts:resource:gauge
  expr: redis_connected_clients
  labels:
    asserts_resource_type: client_connections
    asserts_entity_type: Service
    asserts_source: redis_exporter
```
