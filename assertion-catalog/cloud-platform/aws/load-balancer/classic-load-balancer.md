# Classic Load Balancer

### Setup

#### **Asserts AWS Exporter**

The [Asserts AWS Exporter](https://github.com/asserts/aws-cloudwatch-exporter) is required to discover the Classic Load Balancers. To run the exporter in the development environment

```
docker run -p 8010:8010 --env-file env.properties asserts/aws-cloudwatch-exporter.dev 
```

where env.properties has the AWS credentials.

### Key Performance Indicators (KPIs) and Alerts

The AWS Exporter discovers the Classic Load Balancer and also converts the CloudWatch alarms into metrics. If you are using Asserts, the CloudWatch alarms are converted into alerts.

### KPI Dashboard

The Classic Load Balancer KPI Dashboard shows the following KPIs

* Request Rate
  * **Sum(RequestCount\_ELB)**
* Error Rate
  * **Sum(HttpCode\_ELB\_4XX)**
  * **Sum(HttpCode\_ELB\_5XX)**
  * **Sum(HttpCode\_Backend\_4XX)**
  * **Sum(HttpCode\_Backend\_5XX)**
* Latency
  * **Avg(RequestTime\_ELB)**
* Healthy Hosts
  * **Avg(HealthyHostCount)**
  * **Avg(UnHealthyHostCount)**
* Connection Count, Surge Queue Length and Spill over count
  * **Sum(RejectedConnectionCount)**
  * **Sum(TargetConnectionErrorCount)**
  * **Avg(SurgeQueueLength)**
  * **Avg(SpillOverCount)**

![](../../../../.gitbook/assets/AWS\_ELB.png)
