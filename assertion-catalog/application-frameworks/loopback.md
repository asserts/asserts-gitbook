# Loopback

### Setup

You can follow the below steps to enable Prometheus metrics for Loopback

```
Install
--------
npm install --save @loopback/metrics

Import
--------
import {MetricsComponent} from '@loopback/metrics';

In the constructor, add the component to your application:
----
this.component(MetricsComponent);
```

You can refer the [Link](https://github.com/loopbackio/loopback-next/tree/master/extensions/metrics) for any further config changes or information

You can verify the loopback is enabled by checking following metrics

* loopback\_invocation\_total
* loopback\_invocation\_duration\_seconds

### Metrics

| **Metric**                                                | **KPI**                                                                                                                                                                                                                                                                       |
| --------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Requests</p><p>loopback_invocation_total</p>           | <p>Request Rate</p><p>rate(loopback_invocation_total[5m])</p>                                                                                                                                                                                                                 |
| <p>Errors</p><p>loopback_invocation_total</p>             | <p>Error Ratio (server errors)</p><p>rate(loopback_invocation_total{status=<del>"5.."}[5m])/ rate(loopback_invocation_total[5m])</del></p><p>Error Ratio (client errors)</p><p>rate(loopback_invocation_total{statusCode="4.."}[5m])/ rate(loopback_invocation_total[5m])</p> |
| <p>Latency</p><p>loopback_invocation_duration_seconds</p> | <p>Latency Average</p><p>rate(loopback_invocation_duration_seconds[5m])/ rate(loopback_invocation_total[5m])</p><p>Latency P99</p><p>histogram_quantile (<br>0.99,<br>sum(rate(loopback_invocation_duration_histogram_bucket[1m]) > 0)<br>by (le)<br>)</p>                    |

### Alerts

| **KPI**                                  | **Alerts**                                                                                                                              |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Request Rate                             | **RequestRateAnomaly**                                                                                                                  |
| Error Rate                               | <p><strong>ErrorRatioBreach</strong></p><p><strong>ErrorBuildup</strong> based on a 99.9 SLO</p>                                        |
| <p>Latency Average</p><p>Latency P99</p> | <p><strong>LatencyAverageBreach</strong></p><p><strong>LatencyAverageAnomaly</strong></p><p><strong>LatencyP99ErrorBuildup</strong></p> |

### Dashboards

#### Service KPI Dashboard

This dashboard has the following KPIs from resources and requests:

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

![](<../../.gitbook/assets/dashboard-kpi (2).png>)
