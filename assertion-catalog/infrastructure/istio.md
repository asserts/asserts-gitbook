# Istio

### Setup

Prometheus metrics for Istio can be enabled by following the setup instructions documented [here](https://istio.io/latest/docs/ops/integrations/prometheus/). Once the exporter is set up, check the following metrics to verify the setup:

* istio\_build

### Metrics

#### Request, Errors, and Latency

| **Metric**                                                         | **KPI**                                                                                                                                                                                                                                                                                                    |
| ------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Request Counter</p><p>istio_requests_total</p>                  | <p>Request Rate</p><p>rate(istio_requests_total{reporter="destination"}[5m])</p>                                                                                                                                                                                                                           |
| <p>Error Counter</p><p>istio_requests_total</p>                    | <p>Error Ratio (server errors)</p><p>rate(istio_requests_total{reporter="destination", response_code=~"5.."}[5m])/ rate(istio_requests_total[5m])</p><p>Error Ratio (client errors)</p><p>rate(istio_requests_total{reporter="destination", response_code=~"4.."}[5m])/ rate(istio_requests_total[5m])</p> |
| <p>Latency Histogram</p><p>istio_request_duration_milliseconds</p> | <p>Latency Average</p><p>rate(istio_request_duration_milliseconds_sum[5m])/ rate(istio_request_duration_milliseconds_count[5m])</p><p>Latency P99</p><p>histogram_quantile (<br>0.99,<br>sum(rate(istio_request_duration_milliseconds_bucket[5m]) > 0)<br>by (le)<br>)</p>                                 |

#### Resource

| **Metric**                                                                                                                 | **KPI**                                                                                                  |
| -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| <p>Network Bytes Received</p><p>istio_request_bytes_sum</p><p>Network Bytes Transmitted</p><p>istio_response_bytes_sum</p> | <p>Data transfer rate</p><p>rate(istio_request_bytes_sum[5m)</p><p>rate(istio_response_bytes_sum[5m)</p> |

### **Alerts**

| **KPI**                                  | **Alert**                                                                                                                               |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Request Rate                             | **RequestRateAnomaly**                                                                                                                  |
| Error Rate                               | <p><strong>ErrorRatioBreach</strong></p><p><strong>ErrorBuildup</strong> based on a 99.9 SLO</p>                                        |
| <p>Latency Average</p><p>Latency P99</p> | <p><strong>LatencyAverageBreach</strong></p><p><strong>LatencyAverageAnomaly</strong></p><p><strong>LatencyP99ErrorBuildup</strong></p> |
| Network Bytes                            | **ResourceRateAnomaly**                                                                                                                 |

### KPI Dashboard

Istio KPI Dashboard shows all the above mentioned KPIs

![](<../../.gitbook/assets/istio-kpi-dash.png>)
