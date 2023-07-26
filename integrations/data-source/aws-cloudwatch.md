---
description: Asserts AWS CloudWatch
---

# AWS

Asserts can monitor AWS Services, including Serverless technologies like AWS Lambda and AWS ECS Fargate. The following Asserts components must be installed to start monitoring your AWS Services.

* **Asserts AWS Exporter** enabled in Self-Hosted mode or alternatively installed as an ECS Service.
* **Asserts Layer** for NodeJS and Python AWS Lambda functions. These layers export CPU,  Memory, and other runtime metrics of the Lambda function
* **Asserts ECS Exporter** installed as a sidecar container in each ECS Task definition. This sidecar exports CPU, Memory, and Network metrics for the ECS Task.

Here is a diagram that shows how these components function

<figure><img src="../../.gitbook/assets/AWS_Monitoring_Architecture_Nicer (1).png" alt=""><figcaption><p>Asserts Components for AWS Monitoring</p></figcaption></figure>
