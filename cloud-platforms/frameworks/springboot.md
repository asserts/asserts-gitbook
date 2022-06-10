# Springboot

### Setup

[Spring Boot](https://spring.io/projects/spring-boot) is a popular Java framework for building modern applications. With the help of the [actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html) module and the [micrometer](https://micrometer.io/) library, we can configure a Spring Boot application to expose performance metrics in the [Prometheus format](https://prometheus.io/docs/concepts/data\_model/).

First, you will need the actuator module to enable all the[ management endpoints](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.endpoints.exposing), and the micrometer module to provide the prometheus endpoint that exposes Prometheus metrics.

```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-actuator',
    Implementation ‘io.micrometer:micrometer-registry-prometheus’
}
```

Second, make sure the prometheus endpoint is enabled in your application.properties or application.yml:

```
management.endpoints.web.exposure.include=info, health, prometheus
management.endpoint.prometheus.enabled=true
```

Now if you hit the /actuator/prometheus endpoint of your web application, you will see a list of metrics like these:

```
jvm_memory_used_bytes{area="heap",id="PS Survivor Space",} 2.012508E7
…
http_server_requests_seconds_count{exception="None",method="GET",outcome="SUCCESS",status="200",uri="/actuator/metrics",} 1.0
http_server_requests_seconds_sum{exception="None",method="GET",outcome="SUCCESS",status="200",uri="/actuator/metrics",} 0.084955379
…
http_client_requests_seconds_count{clientName="chief.tsdb.dev.asserts.ai",method="POST",outcome="SUCCESS",status="200",uri="/select/0/prometheus/api/v1/query",} 4785.0
http_client_requests_seconds_sum{clientName="chief.tsdb.dev.asserts.ai",method="POST",outcome="SUCCESS",status="200",uri="/select/0/prometheus/api/v1/query",} 238.762194814
…
```

### Metrics

| **Metric**                                                      | **KPI**                                                                                                                                                                                                                                                                                              |
| --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Request Counter</p><p>http_server_requests_seconds_count</p> | <p>Request Rate</p><p>rate(http_server_requests_seconds_count[5m])</p>                                                                                                                                                                                                                               |
| <p>Error Counter</p><p>http_server_requests_seconds_count</p>   | <p>Error Ratio (server errors)</p><p>rate(http_server_requests_seconds_count{status=~"5.."}[5m])/ rate(http_server_requests_seconds_count[5m])</p><p>Error Ratio (client errors)</p><p>rate(http_server_requests_seconds_count{status=~"4.."}[5m])/ rate(http_server_requests_seconds_count[5m])</p> |
| <p>Latency Histogram</p><p>http_server_requests_seconds</p>     | <p>Latency Average</p><p>rate(http_server_requests_seconds_sum[5m])/ rate(http_server_requests_seconds_count[5m])</p><p>Latency P99</p><p>histogram_quantile (<br>0.99,<br>sum(rate(http_server_requests_seconds_bucket[5m]) > 0)<br>by (le)<br>)</p>                                                |

### Alerts

| **KPI**                                  | **Alerts**                                                                                                                              |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Request Rate                             | **RequestRateAnomaly**                                                                                                                  |
| Error Rate                               | <p><strong>ErrorRatioBreach</strong></p><p><strong>ErrorBuildup</strong> based on a 99.9 SLO</p>                                        |
| <p>Latency Average</p><p>Latency P99</p> | <p><strong>LatencyAverageBreach</strong></p><p><strong>LatencyAverageAnomaly</strong></p><p><strong>LatencyP99ErrorBuildup</strong></p> |

### Dashboards

#### Service KPI Dashboard

This dashboard has the following KPIs:

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

![](<../../.gitbook/assets/image (11).png>)

#### JVM Micrometer KPI Dashboard

This dashboard has the following KPIs:

* JVM memory
* CPU-Usage, Load, Threads, Thread States, File Descriptors, Log Events
* JVM Memory Pools (Heap, Non-Heap)
* Garbage Collection
* Classloading
* Direct-/Mapped-Buffer

![](<../../.gitbook/assets/image (20).png>)
