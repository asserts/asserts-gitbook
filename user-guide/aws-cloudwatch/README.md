---
description: Asserts AWS CloudWatch
---

# AWS CloudWatch

Asserts can monitor AWS Services, including Serverless technologies like AWS Lambda and AWS ECS Fargate. The following Asserts components must be installed to start monitoring your AWS Services.

* **Asserts AWS Exporter** installed as an ECS Service.
* **Asserts Layer** for NodeJS and Python AWS Lambda functions. These layers export CPU,  Memory, and other runtime metrics of the Lambda function
* **Asserts ECS Exporter** installed as a sidecar container in each ECS Task definition. This sidecar exports CPU, Memory, and Network metrics for the ECS Task.

Here is a diagram that shows how these components function

<figure><img src="../../.gitbook/assets/AWS_Monitoring_Architecture.png" alt=""><figcaption><p>Asserts Components for AWS Monitoring</p></figcaption></figure>

#### Installing the Asserts AWS Exporter

The Asserts AWS Exporter must be installed on each AWS Account that needs to be monitored. Asserts provides a CloudFormation template to install the exporter. The link, **install AWS Exporter** as shown in the image below **** will launch the CloudFormation template to install the AWS Exporter.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p>Link to launch CloudFormation template for AWS Exporter installation</p></figcaption></figure>

In addition to installing the exporter, the CloudFormation template also creates an API key to allow the Asserts server to read CloudWatch metrics from this account.&#x20;

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>CloudFormation output</p></figcaption></figure>

After installing the exporter, add the AWS account along with the API key.

#### Installing Asserts Lambda Layer

Asserts provides Lambda layers for [NodeJS](https://github.com/asserts/asserts-aws-lambda-layer-js) and [Python](https://github.com/asserts/aws-lambda-layer-python) to monitor CPU, Memory, and other runtime aspects. To add these layers to your functions, refer to the README of these GitHub projects.

#### Installing the Asserts ECS sidecar container&#x20;

The ECS sidecar container needs to be included in the task definition of each ECS Task. Here is a sample JSON excerpt of the sidecar container definition that can be added to your existing task definition JSON.

```
{
  "logConfiguration": {
    "logDriver": "awslogs",
    "secretOptions": null,
    "options": {
      "awslogs-group": "<GROUP-NAME>",
      "awslogs-region": "<REGION>",
      "awslogs-stream-prefix": "ecs-dockerstats-exporter"
    }
  },
  "portMappings": [
    {
      "hostPort": 8014,
      "protocol": "tcp",
      "containerPort": 8014
    }
  ],
  "image": "asserts/ecs-dockerstats-exporter:v0.9.0.8",
  "essential": true,
  "name": "ecs-dockerstats-exporter"
}
```
