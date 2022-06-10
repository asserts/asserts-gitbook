# Kafka

## Kafka Server <a href="#kafka-server" id="kafka-server"></a>

### Setup <a href="#setup" id="setup"></a>

Kafka server can be enabled for Prometheus metrics either using an external exporter or JMX exporter.

* Kafka exporter can be set up using instructions mentioned here - [Exporter](https://github.com/danielqsj/kafka\_exporter)

> docker run -ti --rm -p 9308:9308 danielqsj/kafka-exporter --kafka.server=kafka:9092 \[--kafka.server=another-server ...]

&#x20;

* JMX Exporter can be set up and configured using [JMX Exporter](https://github.com/prometheus/jmx\_exporter) , while launching the Kafka server you can use the below command to launch

> `java -javaagent:./jmx_prometheus_javaagent-0.16.1.jar=8080:config.yaml -jar yourJar.jar`

After the Kafka server is successfully enabled for the Prometheus metric, you can verify whether you are able to see some of the following metrics in Prometheus.

&#x20;

`1# HELP kafka_topic_partitions Number of partitions for this Topic 2# TYPE kafka_topic_partitions gauge 3kafka_topic_partitions{topic="__consumer_offsets"} 50 4 5# HELP kafka_topic_partition_current_offset Current Offset of a Broker at Topic/Partition 6# TYPE kafka_topic_partition_current_offset gauge 7kafka_topic_partition_current_offset{partition="0",topic="__consumer_offsets"} 0 8 9# HELP kafka_topic_partition_oldest_offset Oldest Offset of a Broker at Topic/Partition 10# TYPE kafka_topic_partition_oldest_offset gauge 11kafka_topic_partition_oldest_offset{partition="0",topic="__consumer_offsets"} 0`

### Metrics <a href="#metrics" id="metrics"></a>

| **Metric**                                                                                                                                                                                                                                                                                            | **Key Performance Indicator(KPI)**                                                                                                                                                           |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Requests</p><p><code>kafka_server_brokertopicmetrics_totalproducerequests_total</code></p><p><code>kafka_server_brokertopicmetrics_messagesin_total</code></p><p><code>kafka_server_brokertopicmetrics_totalfetchrequests_total</code></p><p><code>kafka_topic_partition_current_offset</code></p> | <p>Request Rate</p><p><code>rate(kafka_server_brokertopicmetrics_totalproducerequests_total[5m])</code></p><p><code>rate(kafka_server_brokertopicmetrics_messagesin_total[5m])</code></p>    |
| <p>Errors</p><p><code>kafka_server_brokertopicmetrics_failedfetchrequests_total</code></p><p><code>kafka_server_brokertopicmetrics_failedproducerequests_total</code></p>                                                                                                                             | <p>Error Ratio</p><p><code>rate(kafka_server_brokertopicmetrics_failedfetchrequests_total[5m])</code>/ <code>rate(kafka_server_brokertopicmetrics_totalproducerequests_total[5m])</code></p> |
| <p>Latency</p><p><code>kafka_network_requestmetrics_totaltimems</code></p>                                                                                                                                                                                                                            | <p>Latency P99</p><p><code>kafka_network_requestmetrics_totaltimems{request="Produce", quantile="0.99"} / 1000</code></p>                                                                    |

### Alerts <a href="#alerts" id="alerts"></a>

| **KPI** | **Alerts** |
| ------- | ---------- |

| **KPI**      | **Alerts**                                                                                                    |
| ------------ | ------------------------------------------------------------------------------------------------------------- |
| Request Rate | **RequestRateAnomaly**                                                                                        |
| Error Rate   | <p><strong>ErrorRatioBreach</strong></p><p><strong>ErrorBuildup</strong> based on a <code>99.9</code> SLO</p> |
| Latency P99  | **LatencyP99ErrorBuildup**                                                                                    |

#### Failure Alerts <a href="#failure-alerts" id="failure-alerts"></a>

**KafkaTopicsUnderReplicatedPartitions**

Kafka Partition is not replicate as expected

`kafka_topic_partition_under_replicated_partition` > 0

**KafkaOfflinePartitions**

When Kafka partitions are offline

`kafka_controller_kafkacontroller_offlinepartitionscount > 0`

**KafkaActiveController**

Kafka controller is not active/offline

`kafka_controller_kafkacontroller_activecontrollercount != 1`

**KafkaUnderMinIsrPartitions**

Kafka partitions are under the expected in-sync replicas

`kafka_cluster_partition_underminisr > 0`

### Dashboards <a href="#dashboards" id="dashboards"></a>

The below dashboard shows information about Kafka server metrics

* Messages Produced
* Messages Consumes
* Lag by Consumer
* Partitions for Topics

![](<../../.gitbook/assets/image (8).png>)



## Kafka Client <a href="#kafka-client" id="kafka-client"></a>

### Setup <a href="#setup.1" id="setup.1"></a>

JMX Exporter can be set up and configured using [JMX Exporter](https://github.com/prometheus/jmx\_exporter) , while launching the Kafka client you can use the below command to launch

> `java -javaagent:./jmx_prometheus_javaagent-0.16.1.jar=8080:config.yaml -jar yourJar.jar`

&#x20;

You can check whether following prometheus metrics are available to confirm Kafka client is instrumented

* `kafka_producer_topic_record_send_total`
* `kafka_producer_record_send_total`
* `kafka_consumer_records_consumed_total_records_total`
* `kafka_consumer_fetch_manager_bytes_consumed_total`

### Metrics - Producer <a href="#metrics-producer" id="metrics-producer"></a>

| **Metric**                                                                                                                    | **Key Performance Indicator(KPI)**                                                                                                                    |
| ----------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Requests</p><p><code>kafka_producer_record_send_total</code></p><p><code>kafka_producer_topic_record_send_total</code></p> | <p>Request Rate</p><p><code>rate(kafka_producer_record_send_total[5m])</code></p><p><code>rate(kafka_producer_topic_record_send_total[5m])</code></p> |
| <p>Errors</p><p><code>kafka_producer_record_error_total</code></p><p> </p>                                                    | <p>Error Ratio</p><p><code>rate(kafka_producer_record_error_total[5m])</code>/ <code>rate(kafka_producer_record_send_total[5m])</code></p>            |
| <p>Latency</p><p><code>kafka_producer_request_latency_avg</code></p>                                                          | <p>Latency Average</p><p><code>kkafka_producer_request_latency_avg/ 1000</code></p>                                                                   |

### Metrics - Consumer <a href="#metrics-consumer" id="metrics-consumer"></a>

| **Metric** | **Key Performance Indicator(KPI)** |
| ---------- | ---------------------------------- |

| **Metric**                                                                                                                                                                                                      | **Key Performance Indicator(KPI)**                                                                                                                                                                                                      |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Requests</p><p><code>kafka_consumer_records_consumed_total_records_total</code></p><p><code>kafka_consumer_fetch_total_requests_total</code></p><p><code>kafka_consumer_fetch_manager_fetch_total</code></p> | <p>Request Rate</p><p><code>rate(kafka_consumer_fetch_total_requests_total[5m])</code></p><p><code>rate(kafka_consumer_fetch_total_requests_total[5m])</code></p><p><code>rate(kafka_consumer_fetch_manager_fetch_total[5m])</code></p> |
| <p>Latency</p><p><code>kafka_consumer_fetch_latency_avg_seconds</code></p><p><code>kafka_consumer_fetch_manager_fetch_latency_avg</code></p>                                                                    | <p>Latency Average</p><p><code>kafka_consumer_fetch_latency_avg_seconds</code></p><p><code>kafka_consumer_fetch_manager_fetch_latency_avg / 1000</code></p>                                                                             |

### Alerts <a href="#alerts.1" id="alerts.1"></a>

| **KPI**         | **Alerts**                                              |
| --------------- | ------------------------------------------------------- |
| Request Rate    | RequestRateAnomaly                                      |
| Error Ratio     | <p>ErrorRatioAnomaly<br>ErrorRatioBreach</p>            |
| Latency Average | <p>LatencyAverageBreach</p><p>LatencyAverageAnomaly</p> |

### Dashboards <a href="#dashboards.1" id="dashboards.1"></a>

The following dashboard captures information about both producer and consumer of Kafka client.

It showcases the following information

* Topics connected to producer/consumer
* Producer records
* Producer requests
* Producer latency
* Consumer records
* Consumer Lag

![](<../../.gitbook/assets/image (12).png>)
