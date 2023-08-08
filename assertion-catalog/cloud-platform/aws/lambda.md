# Lambda

#### **Asserts AWS Exporter**

Refer to Asserts [AWS Exporter](../../../integrations/data-source/aws-cloudwatch/aws-exporter.md) and [Lambda Layer](../../../integrations/data-source/aws-cloudwatch/lambda-layer.md) on pre-requires for observing Lambda function.



### Key Performance Indicators (KPIs) and Alerts

#### Request, Errors, and Latency

| **Metric**                                                   | **Key Performance Indicator (KPI)**                                                                              |
| ------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| <p>Request Counter</p><p>aws_lambda_invocations_sum</p>      | <p>Request Rate</p><p>avg_over_time(aws_lambda_invocations_sum[5m])</p>                                          |
| <p>Error Counter</p><p>aws_lambda_errors_sum</p>             | <p>Error Ratio</p><p>avg_over_time(aws_lambda_errors_sum[5m])/ avg_over_time(aws_lambda_invocations_sum[5m])</p> |
| Latency Quantile                                             | aws\_lambda\_duration\_p99                                                                                       |
| <p>Request Throttle Count</p><p>aws_lambda_throttles_sum</p> | <p>Observe throttle count for Sustained Throttling</p><p>aws_lambda_throttles_sum</p>                            |

#### Resource

| **Metric**                                                                                            | **Key Performance Indicator (KPI)**                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Memory NodeJS</p><p>process_heap_bytes</p><p>Memory Python</p><p>process_resident_memory_bytes</p> | <p><strong>NodeJS</strong></p><p>process_heap_bytes / aws_lambda_memory_limit_mb</p><p><strong>Python</strong></p><p>process_resident_memory_bytes / aws_lambda_memory_limit_mb</p>                                                                                 |
| <p>CPU Total Time</p><p><code>process_cpu_seconds_total</code></p>                                    | <p>CPU Time Usage</p><p><code>rate(process_cpu_seconds_total[5m])</code></p>                                                                                                                                                                                        |
| <p>Concurrent Executions</p><p>aws_lambda_concurrent_executions_avg</p>                               | <p>When concurrency is reserved at the function level</p><p>aws_lambda_concurrent_executions_avg / aws_lambda_allocated_concurrency</p><p>For account level</p><p>aws_lambda_concurrent_executions_avg / aws_lambda_account_limit{type="concurrent_executions"}</p> |

**Alerts**

| **KPI**                | **Alert**                                                                                                                   |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Request Rate           | **RequestRateAnomaly**                                                                                                      |
| Error Ratio            | **ErrorRatioBreach** and **ErrorBuildup** based on an availability SLO of 99.9                                              |
| Latency Average        | **LatencyAverageBreach** and **LatencyAverageAnomaly**                                                                      |
| Latency P99            | **LatencyP99ErrorBuildup**                                                                                                  |
| Request Throttle Count | **LambdaFunctionThrottled**                                                                                                 |
| Memory Utilization     | **Saturation** with severity level of **warning** and **critical** when memory utilization exceeds 90% and 95% respectively |
| CPU Time Usage         | **ResourceRateAnomaly**                                                                                                     |
| Network Bytes          | **ResourceRateAnomaly**                                                                                                     |
| Lambda Concurrency     | **Saturation** at Function and Account level                                                                                |

### KPI Dashboard

The Lambda KPI Dashboard shows all the KPIs and also the Cold Starts for correlation with Traffic Spike and Latency degradation

<figure><img src="../../../.gitbook/assets/Lambda_with_Cold_Starts.png" alt=""><figcaption><p>Lambda KPI for a NodeJS Lambda function</p></figcaption></figure>
