# Flask

### Setup

Flask is a python module that can be enabled for Prometheus metrics using flask [Exporter](https://github.com/rycus86/prometheus\_flask\_exporter) for Prometheus.

You can enable your flask code to emit default metrics using the following export code in the python module

```
from prometheus_flask_exporter import PrometheusMetrics
```

In case you want to enable any custom metrics you can follow the steps given in the [Exporter](https://github.com/rycus86/prometheus\_flask\_exporter)

After the flask python module is enabled you can verify by looking at the following default metrics in Prometheus.

* flask\_http\_request\_duration\_seconds
* flask\_http\_request\_total

### Metrics

| **Metric**                                                                                                      | **Key Performance Indicator(KPI)**                                                                                                                                                                                                                                                |
| --------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Requests</p><p>flask_http_request_duration_seconds_count</p>                                                 | <p>Request Rate</p><p>rate(flask_http_request_duration_seconds_count[5m])</p>                                                                                                                                                                                                     |
| <p>Errors</p><p>flask_http_request_duration_seconds_count</p>                                                   | <p>Error Ratio</p><p>rate(flask_http_request_duration_seconds_count{status=~"5.."}[5m])/ rate(flask_http_request_duration_seconds_count[5m])</p>                                                                                                                                  |
| <p>Latency</p><p>flask_http_request_duration_seconds_bucket</p><p>flask_http_request_duration_seconds_count</p> | <p>Latency Average</p><p>rate(flask_http_request_duration_seconds_count[5m])/ rate(flask_http_request_duration_seconds_count[5m])</p><p>Latency P99</p><pre><code>histogram_quantile (
  0.99,
  sum(rate(flask_http_request_duration_seconds_bucket[1m])) by (le)
)</code></pre> |

### Alerts

| **KPI**                                  | **Alerts**                                                                                                                              |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Request Rate                             | **RequestRateAnomaly**                                                                                                                  |
| Error Rate                               | <p><strong>ErrorRatioBreach</strong></p><p><strong>ErrorBuildup</strong> based on a 99.9 SLO</p>                                        |
| <p>Latency Average</p><p>Latency P99</p> | <p><strong>LatencyAverageBreach</strong></p><p><strong>LatencyAverageAnomaly</strong></p><p><strong>LatencyP99ErrorBuildup</strong></p> |

### Dashboards

#### Service KPI Dashboard

This dashboard has the following KPIs that include resource and requests metrics:

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

![](<../../.gitbook/assets/image (23).png>)

You can include any custom dashboard from the community for example [this](https://github.com/rycus86/prometheus\_flask\_exporter/blob/master/examples/sample-signals/grafana/dashboards/example.json) which shows flask specific metrics directly as a custom dashboard for your service.

![](<../../.gitbook/assets/image (17).png>)
