# Redis

### Setup <a href="#setup" id="setup"></a>

Prometheus metrics for redis can be enabled using [redis exporter](https://github.com/oliver006/redis\_exporter). Once the exporter is setup check the following metrics to verify the setup:

* redis\_up
* redis\_uptime\_in\_seconds

### Metrics <a href="#metrics" id="metrics"></a>

#### Request, Errors, and Latency <a href="#request-errors-and-latency" id="request-errors-and-latency"></a>

| **Metric**                                                                                                         | **KPI**                                                                                                                                |
| ------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Request Counter</p><p><code>redis_commands_total</code></p><p><code>redis_connections_received_total</code></p> | <p>Request Rate</p><p><code>rate(redis_commands_total[5m])</code></p><p><code>rate(redis_connections_received_total[5m])</code></p>    |
| <p>Error Counter</p><p><code>redis_rejected_connections_total</code></p>                                           | <p>Error Ratio</p><p><code>rate(request_connections[5m])</code>/ <code>rate(redis_connections_received_total[5m])</code></p>           |
| <p>Latency Counter</p><p><code>redis_commands_duration_seconds_total</code></p>                                    | <p>Latency Average</p><p><code>rate(redis_commands_duration_seconds_total[5m])</code>/ <code>rate(redis_commands_total[5m])</code></p> |

#### Resource <a href="#resource" id="resource"></a>

| **Metric**                                                                                                                                                   | **KPI**                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| <p>CPU Usage</p><p><code>redis_cpu_user_seconds_total</code></p><p><code>redis_cpu_sys_seconds_total</code></p>                                              | `rate(redis_cpu_user_seconds_total[5m]) + rate(redis_cpu_sys_seconds_total[5m])`                                                           |
| <p>Memory Usage</p><p><code>redis_memory_used_rss_bytes</code></p>                                                                                           | `redis_memory_used_rss_bytes / redis_memory_max_bytes`                                                                                     |
| <p>Network Bytes Received</p><p><code>redis_net_input_bytes_total</code></p><p>Network Bytes Transmitted</p><p><code>redis_net_output_bytes_total</code></p> | <p>Data transfer rate</p><p><code>rate(redis_net_input_bytes_total[5m)</code></p><p><code>rate(redis_net_output_bytes_total[5m)</code></p> |
| <p>Current Redis Connected clients</p><p><code>redis_connected_clients</code></p>                                                                            | `redis_connected_clients / redis_config_maxclients`                                                                                        |

### **Alerts** <a href="#alerts" id="alerts"></a>

| **KPI**            | **Alert**                                                                                                                                                                                                                                                                                   |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Request Rate       | **RequestRateAnomaly**                                                                                                                                                                                                                                                                      |
| Error Ratio        | **ErrorRatioBreach** and **ErrorBuildup** based on an availability SLO of `99.9`                                                                                                                                                                                                            |
| Latency Average    | **LatencyAverageBreach** and **LatencyAverageAnomaly**                                                                                                                                                                                                                                      |
| CPU Usage          | **Saturation** with severity level of **warning** and **critical** when cpu utilization exceeds 70% and 90% respectively                                                                                                                                                                    |
| Memory Usage       | **Saturation** with severity level of **warning** and **critical** when memory utilization exceeds 65% and 75% respectively                                                                                                                                                                 |
| Network Bytes      | **ResourceRateAnomaly**                                                                                                                                                                                                                                                                     |
| Client Connections | <p><strong>Saturation</strong> with severity level of <strong>warning</strong> and <strong>critical</strong> when it exceeds 80% and 90% respectively</p><p><strong>ResourceMayExhaust</strong> if connections are about to exceed the limit of 256 connections within the next 4 hours</p> |

#### Failure Alerts <a href="#failure-alerts" id="failure-alerts"></a>

**RedisDown**

Redis instance is down

`1redis_up != 1`

**RedisUptimeReset**

Redis instance restarted

`1delta(redis_uptime_in_seconds[5m]) < 0`

**RedisMasterLinkDown**

Redis master link down

`1( 2 avg_over_time(redis_master_link_up[10m]) 3 and on (instance) 4 redis_instance_info{role="slave"} 5) == 0`

**RedisReplicationBroken**

Redis instance lost a replica

`1delta(redis_connected_slaves[1m]) < 0`

**RedisClusterFlapping**

Changes have been detected in Redis replica connection

`1changes(redis_connected_slaves[5m]) > 2`

**RedisRejectedConnections**

Some connections to Redis have been rejected

`1rate(redis_rejected_connections_total[1m]) * 60 > 0`

**RedisMissingMaster**

Redis Master Missing

`1count by (job, service, redis_mode, namespace,) 2 (redis_instance_info{role="master"}) == 0`

**RedisTooManyMasters**

Standalone and HA setup should only have one master

`1count by (job, service, namespace) 2 (redis_instance_info{role="master", redis_mode="standalone"}) > 1`

**RedisTooFewMastersInCluster**

Redis cluster mode should have every instance in the role of "master"

`1avg by (job, service, namespace) (redis_cluster_size) 2- 3count by (job, service, namespace) 4 (redis_instance_info{role="master", redis_mode="cluster"}) 5> 0`

### KPI Dashboard <a href="#kpi-dashboard" id="kpi-dashboard"></a>

Redis KPI Dashboard shows all the above mentioned KPIs

![](<../../.gitbook/assets/image (13).png>)

