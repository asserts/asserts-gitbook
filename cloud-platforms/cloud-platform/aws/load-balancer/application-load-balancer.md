# Application Load Balancer

### Setup

#### **Asserts AWS Exporter**

The [Asserts AWS Exporter](https://github.com/asserts/aws-cloudwatch-exporter) is required to discover the Load Balancers. To run the exporter in the development environment

```
docker run -p 8010:8010 --env-file env.properties asserts/aws-cloudwatch-exporter.dev 
```

where env.properties has the AWS credentials.

### Key Performance Indicators (KPIs) and Alerts

The AWS Exporter discovers the Load Balancer and also converts the CloudWatch alarms into metrics. If you are using Asserts, the CloudWatch alarms are converted into alerts.

### KPI Dashboard

The Application Load Balancer KPI Dashboard shows the following KPIs

* Request Rate
  * **Sum(RequestCount)**
  * **Sum(RequestCount)** by **TargetGroup**
* Error Rate
  * **Sum(HttpCode\_ELB\_4XX)**
  * **Sum(HttpCode\_ELB\_5XX)**
* Latency
  * **Avg(TargetResponseTime)**
  * **Avg(TargetResponseTime)** by **TargetGroup**
* Connection Counts
  * **Sum(ActiveConnectionCount)**
  * **Sum(NewConnectionCount)**
  * **Sum(RejectedConnectionCount)**
  * **Sum(TargetConnectionErrorCount)**
* TLS Negotiation Error Count
  * **Sum(ClientTLSNegotiationErrorCount)**
  * **Sum(TargetTLSNegotiationErrorCount)**
* Capacity Consumption
  * **Sum(ConsumedLCUUnits)**
  * **Sum(ConsumedLBCapacityUnits**
* Processed Bytes
  * **Sum(ProcessedBytes)**
* Rule Evaluations
  * **Sum(RuleEvaluations)**
