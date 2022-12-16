# Table of contents

* [Features](README.md)

## Getting Started

* [Introduction](<README (1).md>)
* [Self-Hosted](getting-started/self-hosted/README.md)
  * [Helm Chart](getting-started/self-hosted/helm-chart.md)
  * [Docker Compose](getting-started/self-hosted/docker-compose.md)
  * [AWS CloudFormation](getting-started/self-hosted/aws-cloudformation.md)
* [Cloud](getting-started/cloud.md)
* [FAQ](getting-started/faq.md)

## Integrations

* [Logs](integrations/logs.md)
* [Data Source](integrations/data-source/README.md)
  * [Prometheus](integrations/data-source/prometheus.md)
  * [AWS CloudWatch](integrations/data-source/aws-cloudwatch.md)

## User Guide

* [Top Insights](user-guide/top-insights.md)
* [Entity Graph](user-guide/exploring-the-entity-graph.md)
* [SLOs](user-guide/slos.md)
* [RCA Workbench](user-guide/workbench.md)
* [Alerts / Rules](user-guide/assertion-management.md)
* [Monitoring as Code](user-guide/monitoring-as-a-code.md)
* [Global Settings](user-guide/global-settings/README.md)
  * [Relabeling](user-guide/global-settings/relabeling.md)
  * [Custom Model Rules](user-guide/global-settings/custom-model-rules.md)
  * [Authentication (SSO)](user-guide/global-settings/authentication-sso.md)
  * [User Roles](user-guide/global-settings/user-roles.md)

## Assertion Catalog

* [Overview](assertion-catalog/overview.md)
* [Infrastructure](assertion-catalog/infrastructure/README.md)
  * [Kubernetes](assertion-catalog/infrastructure/kubernetes.md)
  * [Node](assertion-catalog/infrastructure/node.md)
  * [Istio](assertion-catalog/infrastructure/istio.md)
* [Cloud Platform](assertion-catalog/cloud-platform/README.md)
  * [AWS](assertion-catalog/cloud-platform/aws/README.md)
    * [Lambda](assertion-catalog/cloud-platform/aws/lambda.md)
    * [ECS](assertion-catalog/cloud-platform/aws/ecs.md)
    * [Load Balancer](assertion-catalog/cloud-platform/aws/load-balancer/README.md)
      * [Application Load Balancer](assertion-catalog/cloud-platform/aws/load-balancer/application-load-balancer.md)
      * [Classic Load Balancer](assertion-catalog/cloud-platform/aws/load-balancer/classic-load-balancer.md)
    * [ApiGateway](assertion-catalog/cloud-platform/aws/apigateway.md)
* [Data Stores](assertion-catalog/data-stores/README.md)
  * [MySQL](assertion-catalog/data-stores/mysql.md)
  * [PostgreSQL](assertion-catalog/data-stores/postgresql.md)
  * [Redis](assertion-catalog/data-stores/redis.md)
* [Application Frameworks](assertion-catalog/application-frameworks/README.md)
  * [Springboot](assertion-catalog/application-frameworks/springboot.md)
  * [Flask](assertion-catalog/application-frameworks/flask.md)
  * [Express](assertion-catalog/application-frameworks/express.md)
  * [Loopback](assertion-catalog/application-frameworks/loopback.md)
  * [gRPC](assertion-catalog/application-frameworks/grpc.md)
* [Messaging Frameworks](assertion-catalog/messaging-frameworks/README.md)
  * [Kafka](assertion-catalog/messaging-frameworks/kafka.md)
  * [RabbitMQ](assertion-catalog/messaging-frameworks/rabbitmq.md)
* [Runtimes](assertion-catalog/runtimes/README.md)
  * [Java](assertion-catalog/runtimes/java.md)
  * [NodeJS](assertion-catalog/runtimes/nodejs.md)
  * [GoLang](assertion-catalog/runtimes/golang.md)
* [Extending Asserts Library](assertion-catalog/extending-asserts-library.md)

## How Asserts Works

* [How Asserts Processes Data](how-asserts-works/how-asserts-processes-data.md)
* [Understanding SAAFE Model](how-asserts-works/understanding-saafe-model.md)
