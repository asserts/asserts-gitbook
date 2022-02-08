---
description: How to set up AWS Serverless Monitoring with Asserts
---

# AWS Serverless Monitoring

To monitor AWS Lambda or Fargate, the CloudWatch metrics need to be exported from AWS to Asserts. Asserts provides an exporter to do this.&#x20;

![Export AWS CloudWatch Metrics to Asserts](<../.gitbook/assets/Screenshot 2021-12-01 at 2.49.36 PM (2).png>)

## Set up CloudWatch Exporter on EC2

#### Step 1 Set up IAM Policy, Role, and EC2 Instance profile

Create a stack using a CloudFormation template to set up the necessary IAM policy, role, and EC2 instance profile

```bash
curl -L https://raw.githubusercontent.com/asserts/aws-exporter-ec2/main/AWSCloudFormationTemplate.json --output AWSCloudFormationTemplate.json
AWS cloudformation create-stack \
    --stack-name asserts-aws-exporter \
    --template-body file://$PWD/AWSCloudFormationTemplate.json \
    --capabilities CAPABILITY_NAMED_IAM
```

#### Step 2 Create an EC2 Instance

Create a `t2.micro` EC2 instance with Ubuntu 18.0.4 and assign the IAM Instance profile `asserts-aws-exporter-role`. ssh into the instance. Run the following command

`sudo apt-get update`

#### Step 3 Download the Asserts Exporter bundle

```
curl -L https://github.com/asserts/aws-exporter-ec2/releases/download/1.0.97/aws-exporter-ec2.tar.gz --compressed --output aws-exporter-ec2.tar.gz
tar -xf aws-exporter-ec2.tar.gz
cd ansible
```

#### Step 4 Configure the AWS Region that needs to be scraped

Configure the AWS region in `aws-cloudwatch-exporter/conf/`cloudwatch\_scrape\_config\_sample.yml

```
regions:
  - us-west-2
```

#### Step 5 Configure export of CloudWatch log message as a metric

In the Lambda metric configuration, you can add a configuration to convert a CloudWatch log message into a Prometheus metric under the `logs` config element. You can specify a regular expression with capturing groups and export the groups as labels.&#x20;

```
  - name: AWS/Lambda
    dimensionFilters:
      FunctionName: .+
    metrics:
      ...
    logs:
      - lambdaFunctionName: (Function.+)|(OrderProcessor)
        logFilterPattern: "About to put message in SQS Queue"
        regexPattern: ".*put message in SQS Queue https://sqs.us-west-2.amazonaws.com/342994379019/(.+)"
        labels:
          "destination_type": "SQSQueue"
          "destination_name": "$1"
```

When the following CloudWatch log message is logged by the Lambda function `OrderProcessor`

```
About to put message in SQS Queue https://sqs.us-west-2.amazonaws.com/342994379019/ShipmentRequest
```

the `logs` configuration will result in the creation of the following metric

```
aws_lambda_logs{d_function_name="OrderProcessor", d_destination_type="SQSQueue", d_destination_name="ShipmentRequest", region="us-west-"}
```

Using this metric, Asserts will automatically establish a relationship between the `OrderProcessor` function and the `ShipmentRequest` SQS Queue in the entity graph. Note that Asserts makes it possible to establish relationships between entities using any metric as long as the metric has labels to identify the entities. The above example is what Asserts will do out-of-the-box. If you have some other metric or want to put additional information in the `aws_lambda_logs` metric, you can do so and then customize how the relationship is established.

#### Step 6 Enable monitoring for ECS/AWS Fargate (Optional)

If you have containerized applications running on ECS/Fargate, you can enable monitoring for it. If your application is instrumented with Prometheus, you need to enable ECS task discovery by setting the following configuration&#x20;

```
discoverECSTasks: true
```

Please note that in addition to the above configuration, you may have to export CloudWatch metrics for resource monitoring. This depends on whether your application is instrumented using Prometheus and which resource metrics are exported by the Prometheus client. To enable exporting resource metrics from CloudWatch, you need to uncomment the following commented configuration -

```
#  - name: ECS/ContainerInsights
#    dimensionFilters:
#      ServiceName: .+
#    metrics:
#      - name: MemoryReserved
#        stats:
#          - Average
#          - Maximum
#      - name: MemoryUtilized
#        stats:
#          - Average
#          - Maximum
#      - name: CpuReserved
#        stats:
#          - Average
#          - Maximum
#      - name: CpuUtilized
#        stats:
#          - Average
#          - Maximum
#      - name: NetworkRxBytes
#        stats:
#          - Sum
#      - name: NetworkTxBytes
#        stats:
#          - Sum
#      - name: StorageReadBytes
#        stats:
#          - Sum
#      - name: StorageWriteBytes
#        stats:
#          - Sum
```

#### Step 7 Configure remote-write

Set values for the following placeholders in `configure-services.yaml`

1. `REMOTE-WRITE-URL` Your Asserts remote write URL. E.g., `https://acme.tsdb.asserts.ai/insert/0/prometheus/`
2. `USERNAME/PASSWORD` Your Asserts tenant username and password
3. `TENANT-NAME` Your Asserts tenant name

#### Step 8 Configure Environment name

The environment name needs to be configured in the `configure-services.yaml`. It can be done in one of the following ways

1. `external_labels` Specify your environment name here if the environment cannot be identified from the metric. This is also a simpler option to get started with
2.  `metricRelabelings` Use this option if the environment name is available in one of the metric labels. E.g., if your environment name is present in a tag named `env_name` on your AWS Lambda functions or ECS Services, the metric will have a label called `tag_env_name`. You could then specify the environment as follows&#x20;

    ```
              # __name__ will have the metric name. For e.g. aws_lambda_invocations_sum
              - sourceLabels: [__name__, tag_env_name]
                regex: aws_.+;(.+)
                targetLabel: asserts_env
                action: replace
                replacement: $1 
    ```

_**Note:**_ Please note that only one of the two ways should be configured. If you use metric relabelling to specify the environment, please remove the environment label configuration in `external_labels`

#### Step 9 Start the exporter and vmagent

Start the services by running the following command

```
bash -x install.sh
```

Check `aws-cloudwatch-exporter/aws-exporter.log` to verify that the exporter started without any errors. You should see the following log messages

```
INFO  2021-11-30 16:55:40.442 main StartupInfoLogger Started CloudWatchExporterApplication in 7.251 seconds (JVM running for 7.626)
...
INFO  2021-11-30 16:55:45.449 pool-1-thread-5 MetricScrapeTask BEGIN Scrape for region us-west-2 and interval 300
...
INFO  2021-11-30 16:55:57.088 pool-1-thread-5 MetricScrapeTask END Scrape for region us-west-2 and interval 300
```

#### Step 10 Verify the metrics in Asserts

Verify the presence of metrics in Asserts. E.g., for AWS Lambda you can check for the metric `aws_lambda_timeout_seconds`, and for ECS/Fargate you can check for the metric `aws_ecs_containerinsights_cpu_utilization_avg`

![Lambda metric in Asserts](<../.gitbook/assets/Screen Shot 2021-12-20 at 9.34.15 AM.png>)
