# ApiGateway

### Setup

#### **Asserts AWS Exporter**

The [Asserts AWS Exporter](https://github.com/asserts/aws-cloudwatch-exporter) is required to discover the **ApiGateways**. To run the exporter in the development environment

```
docker run -p 8010:8010 --env-file env.properties asserts/aws-cloudwatch-exporter.dev 
```

where env.properties has the AWS credentials.

### Key Performance Indicators (KPIs) and Alerts

The AWS Exporter discovers the ApiGateway and also converts any CloudWatch alarms firing on it into metrics. If you are using Asserts, the CloudWatch alarms are converted into alerts.

### KPI Dashboard

The ApiGateway KPI Dashboard shows the following KPIs

* Request Rate
  * **Sum(RequestCount)**
* Error Rate
  * **Sum(4XXError)**
  * **Sum(5XXError)**
* Latency
  * **Avg(Latency)**
  * **Max(Latency)**
  * **Avg(IntegrationLatency)**
* Cache Hit/Miss counts
  * **Sum(CacheHitCount)**
  * **Sum(CachMissCount)**
