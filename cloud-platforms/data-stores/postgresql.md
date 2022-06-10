# PostgreSQL

### Setup <a href="#setup" id="setup"></a>

To enable PostgreSQL for the Prometheus metrics you can use the Prometheus exporter for PostgreSQL [Here](https://github.com/prometheus-community/postgres\_exporter)

You can enable exporter using docker to a running PostgreSQL DB instance with the following command

`1docker run \ 2 --net=host \ 3 -e DATA_SOURCE_NAME="postgresql://postgres:password@localhost:5432/postgres?sslmode=disable" \ 4 quay.io/prometheuscommunity/postgres-exporter`

Once it is attached to the DB instance check your Prometheus for the following metrics to verify it is attached properly

* `pg_stat_database_numbackends`
* `pg_stat_database_xact_commit`

### Metrics <a href="#metrics" id="metrics"></a>

#### Requests & Errors <a href="#requests-and-errors" id="requests-and-errors"></a>

| **Metric**                                                                                                                                                      | **Key Performance Indicator (KPI)**                                                                                                                                                               |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Requests</p><p><code>pg_stat_database_xact_commit</code></p><p><code>pg_stat_database_xact_rollback</code></p><p><code>pg_stat_database_blks_read</code></p> | <p>Request Rate</p><p><code>rate(pg_stat_database_xact_commit[5m])</code></p><p><code>rate(pg_stat_database_xact_rollback[5m])</code></p><p><code>rate(pg_stat_database_blks_read[5m])</code></p> |
| <p>Errors</p><p><code>pg_stat_database_conflicts</code></p>                                                                                                     | <p>Error Ratio</p><p><code>rate(pg_stat_database_conflicts[5m])</code>/ <code>rate(pg_stat_database_xact_commit[5m])</code></p>                                                                   |
| <p>Latency</p><p><code>pg_stat_database_blks_hit + pg_stat_database_blks_read</code></p>                                                                        | <p>Latency Average</p><p><code>rate(pg_stat_database_blks_hit + pg_stat_database_blks_read[5m])</code>/ <code>rate(pg_stat_database_blk_read_time[5m])</code></p>                                 |

#### Resource <a href="#resource" id="resource"></a>

| **Metric**                                                                   | **Key Performance Indicator (KPI)**                                                                                                                                                                                        |
| ---------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Connections</p><p><code>pg_stat_database_numbackends</code></p>           | <p>Connections Usage</p><p><code>avg_over_time(pg_stat_database_numbackends[5m)</code></p><p>Connection usage against limit</p><p><code>pg_stat_database_numbackends</code> / <code>pg_settings_max_connections</code></p> |
| <p>Database Disk Usage</p><p><code>pg_database_size_bytes</code></p><p> </p> | <p>Database disk usage limit</p><p><code>predict_linear(pg_database_size_bytes[1h])</code></p>                                                                                                                             |

### **Alerts** <a href="#alerts" id="alerts"></a>

| **KPI**             | **Alert**                                                                                                                                               |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                     |                                                                                                                                                         |
| Request Rate        | **RequestRateAnomaly**                                                                                                                                  |
| Error Ratio         | **ErrorRatioBreach** and **ErrorBuildup** based on an availability SLO of `99.9`                                                                        |
| Latency Average     | **LatencyAverageBreach** and **LatencyAverageAnomaly**                                                                                                  |
| Database Disk Usage | **ResourceMayExhaust**                                                                                                                                  |
| Connections         | **ResourceRateAnomaly** and **Saturation** with severity level of **warning** and **critical** when memory utilization exceeds 80% and 90% respectively |

#### Failure Alerts <a href="#failure-alerts" id="failure-alerts"></a>

**PostgresIsDown**

Postgresql is not running

pg\_up != 1

**PostgresGotRestarted**

Postgresql server restarted

time() - pg\_postmaster\_start\_time\_seconds < 60

**PostgresHasHighDeadLocks**

Postgresql is having too many deadlocks

rate(pg\_stat\_database\_deadlocks\[1m]) \* 60 > 5

**PostgresHasExporterErrors**

Exporter is not running properly

pg\_exporter\_last\_scrape\_error > 0

### Dashboards <a href="#dashboards" id="dashboards"></a>

Following dashboard will show key metrics like

* Uptime
* Transactions
* Active Session
* Max Connection

![](<../../.gitbook/assets/image (10).png>)

You can configure the following [Dashboard](https://grafana.com/grafana/dashboards/9628) as a custom dashboard in case you would like to get very detailed information for each PostgreSQL Prometheus metric.
