# gRPC

### Setup

[gRPC Go](https://github.com/grpc/grpc-go) recently acquired support for Interceptors, i.e. middleware that is executed by a gRPC Server before the request is passed onto the user's application logic. It is a perfect way to implement common patterns: auth, logging, and... monitoring.

To use Interceptors in chains, please see [link](https://github.com/mwitkow/go-grpc-middleware)

This library requires Go 1.9 or later.

For enabling prometheus metrics interceptor in your application you can refer the following [link here](https://github.com/grpc-ecosystem/go-grpc-prometheus)

Once you enable it verify your setup by checking the following metrics

* grpc\_server\_handled\_total
* grpc\_server\_handling\_seconds\_bucket

### Metrics

| **Metric**                                                                                        | **Key Performance Indicator(KPI)**                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Requests</p><p>grpc_server_handled_total</p>                                                   | <p>Request Rate</p><p>rate(grpc_server_handled_total[5m])</p><p> </p>                                                                                                                                                                                                |
| <p>Errors</p><p>grpc_server_handled_total{grpc_code!="OK"}</p>                                    | <p>Error Ratio</p><p>rate(grpc_server_handled_total{grpc_code!="OK"}[5m])/ rate(grpc_server_handled_total[5m])</p>                                                                                                                                                   |
| <p>Latency</p><p>grpc_server_handling_seconds_count</p><p>grpc_server_handling_seconds_bucket</p> | <p>Latency Average</p><p>rate(grpc_server_handling_seconds_count[5m])/ rate(grpc_server_handling_seconds_count[5m])</p><p>Latency P99</p><pre><code>histogram_quantile (
  0.99,
  sum(rate(grpc_server_handling_seconds_bucket[1m])) by (le)
)</code></pre><p> </p> |

&#x20;

### Alerts

| **KPI**                                  | **Alerts**                                                                                                                              |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Request Rate                             | **RequestRateAnomaly**                                                                                                                  |
| Error Rate                               | <p><strong>ErrorRatioBreach</strong></p><p><strong>ErrorBuildup</strong> based on a 99.9 SLO</p>                                        |
| <p>Latency Average</p><p>Latency P99</p> | <p><strong>LatencyAverageBreach</strong></p><p><strong>LatencyAverageAnomaly</strong></p><p><strong>LatencyP99ErrorBuildup</strong></p> |

### Dashboards

#### Service KPI Dashboard

This dashboard has the following KPIs for resources and requests:

* Request Rate
* Latency Average
* Latency P99
* Error Rate
* CPU %
* CPU Cores Used
* CPU Throttle
* Memory %
* Memory Bytes
* Disk Usage
* Network Usage

![](<../../.gitbook/assets/image (7).png>)
