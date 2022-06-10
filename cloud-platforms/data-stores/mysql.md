# MySQL

### Setup <a href="#setup" id="setup"></a>

To enable the Prometheus metric for MySQL you can use MySQL exporter [Here](https://github.com/prometheus/mysqld\_exporter). You can either run MySQL exporter using an environment variable or using the docker.

Using an environment variable

> export DATA\_SOURCE\_NAME='user:password@(hostname:3306)/'\
> ./mysqld\_exporter \<flags>

Using docker

> docker network create my-mysql-network\
> docker pull prom/mysqld-exporter
>
> docker run -d\
> \-p 9104:9104\
> \--network my-mysql-network\
> \-e DATA\_SOURCE\_NAME="user:password@(hostname:3306)/"\
> prom/mysqld-exporter

Please refer to the exporter link above to enable flags in the exporter.

Verify the following metrics in the Prometheus to confirm the exporter is attached to the MySQL server properly.

* `mysql_version_info`
* `mysql_global_status_connections`

### Metrics <a href="#metrics" id="metrics"></a>

#### Request and Errors <a href="#request-and-errors" id="request-and-errors"></a>

| **Metric**                                                                                                              | **Key Performance Indicator (KPI)**                                                                                                                                                        |
| ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| <p>Request</p><p><code>mysql_global_status_connections</code></p><p><code>mysql_global_status_handlers_total</code></p> | <p>Request Rate</p><p><code>rate(mysql_global_status_connections[5m])</code></p><p><code>rate(sum without(handler) (mysql_global_status_handlers_total{handler=~"commit"})[5m])</code></p> |
| <p>Error</p><p><code>mysql_global_status_connection_errors_total</code></p>                                             | <p>Error Ratio</p><p><code>rate(mysql_global_status_connection_errors_total[5m])</code>/ <code>rate(mysql_global_status_connections[5m])</code></p>                                        |

#### Resource <a href="#resource" id="resource"></a>

| **Metric**                                                                                                                                                            | **Key Performance Indicator (KPI)**                                                                                                                                       |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Connections</p><p><code>mysql_global_status_max_used_connections</code></p>                                                                                        | <p>Connection Usage</p><p><code>mysql_global_status_max_used_connections</code> / <code>mysql_global_variables_max_connections</code></p><p> </p>                         |
| <p>Temp Tables Created</p><p><code>mysql_global_status_created_tmp_tables</code></p>                                                                                  | <p>Temp Tables Usage</p><p><code>avg_over_time(mysql_global_status_created_tmp_tables[5m)</code></p>                                                                      |
| <p>Network Bytes Received</p><p><code>mysql_global_status_bytes_received</code></p><p>Network Bytes Transmitted</p><p><code>mysql_global_status_bytes_sent</code></p> | <p>Data transfer rate</p><p><code>avg_over_time(mysql_global_status_bytes_received[5m)</code></p><p><code>avg_over_time(mysql_global_status_bytes_received[5m)</code></p> |
| <p>Open Files</p><p><code>mysql_global_status_open_files</code></p>                                                                                                   | <p>Open Files Usage</p><p><code>mysql_global_status_open_files / mysql_global_variables_open_files_limit</code></p>                                                       |

### **Alerts** <a href="#alerts" id="alerts"></a>

| **KPI**                             | **Alert**                                                                                                            |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
|                                     |                                                                                                                      |
| Request Rate                        | **RequestRateAnomaly**                                                                                               |
| Error Ratio                         | **ErrorRatioBreach** and **ErrorBuildup** based on an availability SLO of `99.9`                                     |
| Connection Usage , Open Files Usage | **Saturation** with severity level of **warning** and **critical** when utilization exceeds 80% and 90% respectively |
| Temp Tables Usage                   | **ResourceRateAnomaly**                                                                                              |
| Network Bytes                       | **ResourceRateAnomaly**                                                                                              |

#### Failure Alerts <a href="#failure-alerts" id="failure-alerts"></a>

**MySQLDown**

MySQL is not Running

mysql\_up != 1

**MySqlRestarted**

MySQL server restarted

mysql\_global\_status\_uptime < 60

**MySqlSlowQueries**

MySQL queries are running slow

rate(mysql\_global\_status\_sloq\_queries\[1m]) \* 60 > 0

**MysqlSlaveSqlThreadNotRunning**

MySQL slave is not running though it is enabled

mysql\_slave\_status\_master\_server\_id > 0 and on (instance, asserts\_env, asserts\_site) mysql\_slave\_status\_slave\_sql\_running == 0

### Dashboards <a href="#dashboards" id="dashboards"></a>

Following MySQL dashboard shows the Summary level metrics

![](<../../.gitbook/assets/image (16).png>)

Further details metrics are shown in the below dashboard

![](<../../.gitbook/assets/image (10).png>)

InnoDB Specific metrics are shown in the below dashboard

![](<../../.gitbook/assets/image (21).png>)
