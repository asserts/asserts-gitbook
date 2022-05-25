---
description: How to set up AWS Serverless Monitoring with Asserts
---

# AWS Serverless Monitoring

To monitor AWS Lambda or Fargate, the CloudWatch metrics need to be exported from AWS to Asserts. Asserts provides an exporter to do this.&#x20;

![Export AWS CloudWatch Metrics to Asserts](<../.gitbook/assets/Screenshot 2021-12-01 at 2.49.36 PM (2).png>)

## Install AWS Exporter on ECS

#### Step 1 Set up AWS Exporter on ECS

The Asserts AWS Exporter can be set up by using a [CloudFormation template](https://s3.us-west-2.amazonaws.com/downloads.asserts.ai/aws-integration/ecs/v3/aws-integration-https.yaml). The following inputs would need to be provided -

**VPC, Subnets, and ALB Configuration**

1. **VPC Id** The VPC in which the ECS Services and the Application Load Balancer need to be installed.&#x20;
2. **Subnets** The subnets for the service instances. At least 2 subnets in different availability zones need to be provided
3. **Hosted Zone** The Rout53 Hosted Zone for the root domain
4. **Subdomain** The subdomain for the ALB under the above Hosted Zone

**ECS Cluster and Log Group Settings**

1. **ECS Cluster Name** The ECS Cluster name under which the asserts exporter services will be created. Defaults to **asserts-aws-integration**
2. **Log Group Name** The CloudWatch log group name under which the logs for the services will be **** stored. Defaults to **asserts-aws-integration**
3. **Log Group Retention Period** The log retention. Defaults to **7 days**
4. **Enable Container Insights** This is an ECS Cluster level setting that will determine if ECS resource utilization metrics need to be captured at the container level. This defaults to **enabled**. This has cost implications so if cost is a concern, it can be set to **disabled**

**Asserts TSDB and API Server Credentials**

1. **Asserts Tenant Name** If your asserts application URL is https://acme.app.asserts.ai, your tenant name is **acme**
2. **Asserts Remote Write URL** For e.g. https://acme.tsdb.asserts.ai
3. **Asserts Remote Write Password** The Asserts remote write password
4. **Asserts API Server URL** For e.g. https://acme.app.asserts.ai
5. **Asserts API Server API User Name** An API key can be created in Asserts. In the Asserts product, Go to your Profile -> Settings -> Credentials and create a new credential. Copy the **Id** and **Secret** for use later. Specify the **Id** as the User name
6. **Asserts API Server API Password** Specify the **Secret** as the password



**Asserts AWS User**

The template would create a user named **asserts-cloudwatch-user** in AWS and also create an API Key for this user. The API id/key would be available in the output of the CloudFormation Stack. Use this id and key to set up the AWS Account in Asserts as shown below&#x20;

![](<../.gitbook/assets/image (8).png>)

#### Step 2 Verify the metrics in Asserts

Verify the presence of metrics in Asserts. E.g., for AWS Lambda you can check for the metric `aws_lambda_timeout_seconds`, and for ECS/Fargate you can check for the metric `aws_ecs_containerinsights_cpu_utilization_avg`

![Lambda metric in Asserts ](<../.gitbook/assets/Screen Shot 2021-12-20 at 9.34.15 AM.png>)

**Step 3 Verify that Asserts ECS Services and Application Load Balancer are discovered**

![](<../.gitbook/assets/image (7).png>)

