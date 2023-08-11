# Application Load Balancer

### Setup

#### **Asserts AWS Exporter**

Refer to [aws-cloudwatch.md](../../../../integrations/data-source/aws-cloudwatch.md "mention") on how to install Asserts AWS Exporter

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

**Dashboard**

Asserts has out-of-the-box dashboard for Application Load Balancer which shows all the key KPIs

<figure><img src="../../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
