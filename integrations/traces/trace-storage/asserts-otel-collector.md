# Asserts OTel Collector

The Asserts OTel Collector helps reduce trace volumes significantly by retaining only slow and error traces. The Asserts OTel Collector needs to be installed in your infrastructure and all the OTel agents would need to be configured to send their traces to the Asserts collector. The Asserts OTel collector can be installed either as an ECS Service or run as a container using docker-compose.

### ECS Service&#x20;

The Asserts collector can be installed as an ECS Service by creating a CloudFormation stack using [this CloudFormation template](https://s3.us-west-2.amazonaws.com/downloads.asserts.ai/asserts-otel-collector/ecs/v1/otel-collector-main-aws.yaml). The collector will send the traces to AWS X-Ray in the region in which it is installed.&#x20;

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption><p>Collector CloudFormation Template</p></figcaption></figure>

#### Template Parameters

<mark style="color:orange;">**Asserts Environment**</mark>

Typically there would be multiple environments. For e.g., `prod`, `stage, dev` etc. The collector should be deployed in each environment. The environment name can be specified through this parameter.&#x20;

<mark style="color:orange;">**Asserts Site**</mark>

In a given environment, there may be deployment in multiple regions. This would normally be the case for production environments. For e.g. there may be multiple AWS regions in which the traces may originate. The collector should be deployed in each region and the region name can be specified through this.

<mark style="color:orange;">**VPC**</mark>

Select the VPC in which the collector is to be installed

<mark style="color:orange;">**ALBType, ALBSubnets and ALBIngressSourceCIDR**</mark>

The ALB type can be `internal` or `internet-facing`. Specify the subnets for the ALB and the CIDR for the ingress.

<mark style="color:orange;">**ECS Cluster, Subnets, Public IP enablement**</mark>

The template will create a new ECS Cluster in which the collector will be installed. Specify the cluster name and the subnets on which the collector needs to be installed. The ECS Task instances should be assigned a public IP. Leave this to `ENABLED`

<mark style="color:orange;">**Docker Image versions**</mark>

The Asserts collector ECS Task has two containers. The OTEL collector and the metric exporter. The latter is a sidecar container gathers container-level CPU, Memory, and Network metrics using the [AWS ECS Task metadata API](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-metadata-endpoint.html) &#x20;

<mark style="color:orange;">**Asserts Server API Configuration**</mark>

The collector retrieves configuration by connecting to the Asserts server. Specify the API URL, username, and password through these parameters

<mark style="color:orange;">**Asserts TSDB Configuration**</mark>

The metrics gathered by the collector and sidecar need to be sent to the Asserts Prometheus TSDB.&#x20;

`Active Mode`

If the remote write URL and credentials are specified, the sidecar container will scrape the span metrics from the collector and remote write the span and container resource metrics to the TSDB. The collector should be run in this mode if the [Asserts AWS Exporter](../../data-source/aws-cloudwatch/aws-exporter.md) is not installed or if ECS Service discovery is not enabled.&#x20;

`Passive Mode`

If the [Asserts AWS Exporter](../../data-source/aws-cloudwatch/aws-exporter.md) is installed and ECS Service discovery is enabled in it, then the remote write URL and credentials can be left blank and the exporter will scrape and remote these metrics.

<mark style="color:orange;">**Number of collectors**</mark>&#x20;

The number of collector instances that need to run.

<mark style="color:orange;">**Log level**</mark>&#x20;

The collector log level. Defaults to `info`. This can be set to `debug` for debugging purposes. Please bear in mind that the log volume could be very high so it's best to do this for short durations and revert back to `info`
