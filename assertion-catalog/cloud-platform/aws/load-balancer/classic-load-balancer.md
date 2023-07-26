# Classic Load Balancer

### Setup

#### **Asserts AWS Exporter**

Refer to [aws-cloudwatch.md](../../../../integrations/data-source/aws-cloudwatch.md "mention") on how to install Asserts AWS Exporter

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
