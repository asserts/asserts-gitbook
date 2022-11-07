# NodeJS

### Setup

The [Prometheus NodeJS Exporter](https://github.com/siimon/prom-client) needs to be set up. For e.g.

```
const client = require('prom-client');
const collectDefaultMetrics = client.collectDefaultMetrics;
const Registry = client.Registry;
const register = new Registry();
collectDefaultMetrics({ register })
```

### Metrics and Key Performance Indicators (KPIs)

| **Metric**                                                     | **KPI**                                                                                                                                                                                                                                                      |
| -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| <p>Memory</p><p><code>process_resident_memory_bytes</code></p> | <p>Utilization in k8s</p><p><code>process_resident_memory_bytes / kube_node_status_allocatable{resource="memory"}</code></p><p>Utilization in non k8s</p><p><code>process_resident_memory_bytes / node_memory_MemTotal_bytes</code></p>                      |
| <p>CPU</p><p><code>process_cpu_seconds_total</code></p>        | <p>Utilization in k8s</p><p><code>rate(process_cpu_seconds_total[5m]) / kube_pod_container_resource_limits{resource="cpu"}</code></p><p>Utilization in non k8s</p><p><code>rate(process_cpu_seconds_total[5m]) / rate(node_cpu_seconds_total[5m])</code></p> |

### Dashboard

In addition to the above KPIs, the following NodeJS runtime metrics are visualized in the KPI Dashboard

#### Heap Usage

* nodejs\_heap\_size\_total\_bytes
* nodejs\_heap\_size\_used\_bytes
* nodejs\_heap\_space\_size\_used\_bytes

#### Event loop Latency

* nodejs\_eventloop\_lag\_seconds
* nodejs\_eventloop\_lag\_p99\_seconds

#### GC Duration and Count

* rate(nodejs\_gc\_duration\_seconds\_sum\[5m])
* rate(nodejs\_gc\_duration\_seconds\_count\[5m])

#### Active Handles and Requests

* nodejs\_active\_handles\_total
* nodejs\_active\_reqeuests\_total

#### File Handles

* process\_open\_fds
* process\_max\_fds

### Dashboard

![](<../../.gitbook/assets/image (14).png>)
