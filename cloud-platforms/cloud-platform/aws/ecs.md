# ECS

### Setup

#### **Asserts AWS Exporter**

The [Asserts AWS Exporter](https://github.com/asserts/aws-cloudwatch-exporter) is required to discover the ECS Service. You can also export metrics from CloudWatch, but it is recommended to instrument your ECS application and configure the AWS Exporter to scrape Prometheus metrics from it. To run the exporter in the development environment

```
docker run -p 8010:8010 --env-file env.properties asserts/aws-cloudwatch-exporter.dev 
```

where env.properties has the AWS credentials.

The discovered ECS targets would be available over the HTTP endpoint http://localhost:8010/ecs-sd-config The HTTP service discovery would need to be configured in your Prometheus

### Key Performance Indicators (KPIs) and Alerts

For the KPIs and Alerts based on the directly scraped metrics, refer to the documentation for the respective runtime and framework.

### KPI Dashboard

The ECS Service KPI Dashboard shows the following KPIs

* Memory Utilization
* CPU Utilization
* Network IO
* Disk IO
* Number of Tasks
* Number of Deployments
