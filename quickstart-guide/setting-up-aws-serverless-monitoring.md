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
curl -L https://github.com/asserts/aws-exporter-ec2/releases/download/1.0.96/aws-exporter-ec2-1.0.96.tar.gz --compressed --output aws-exporter-ec2-1.0.96.tar.gz
tar -xf aws-exporter-ec2-1.0.96.tar.gz
cd ansible
ls -al
total 16
drwxr-xr-x  6 user  staff   192 Nov 29 16:36 .
drwxr-xr-x  3 user  staff    96 Nov 30 17:09 ..
drwxr-xr-x  9 user  staff   288 Nov 29 16:36 aws-cloudwatch-exporter-1.0.96
-rw-r--r--  1 user  staff  1395 Nov 29 16:35 configure-services.yaml
-rw-r--r--  1 user  staff   153 Nov 15 19:36 install.sh
drwxr-xr-x  4 user  staff   128 Nov 15 19:35 roles
```

#### Step 4 Configure the AWS Region that needs to be scraped

Configure the AWS region in `aws-cloudwatch-exporter-1.0.96/cloudwatch-scrape-configuration.yml`

```
regions:
  - us-west-2
```

#### Step 5 Enable monitoring for ECS/AWS Fargate

If your application running on ECS/AWS Fargate is instrumented with Prometheus, you need to enable ECS task discovery by setting the following configuration&#x20;

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

#### Step 6 Configure remote-write

Set values for the following placeholders in `configure-services.yaml`

1. `REMOTE-WRITE-URL` Your Asserts remote write URL. E.g., `https://acme.tsdb.asserts.ai/insert/0/prometheus/`
2. `USERNAME/PASSWORD` Your Asserts tenant username and password
3. `TENANT-NAME` Your Asserts tenant name

#### Step 7 Configure Environment name

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

#### Step 8 Start the exporter and vmagent

Start the services by running the following command

```
bash -x install.sh
```

Check `aws-cloudwatch-exporter-1.0.96/aws-exporter.log` to verify that the exporter started without any errors. You should see the following log messages

```
INFO  2021-11-30 16:55:40.442 main StartupInfoLogger Started CloudWatchExporterApplication in 7.251 seconds (JVM running for 7.626)
...
INFO  2021-11-30 16:55:45.449 pool-1-thread-5 MetricScrapeTask BEGIN Scrape for region us-west-2 and interval 300
...
INFO  2021-11-30 16:55:57.088 pool-1-thread-5 MetricScrapeTask END Scrape for region us-west-2 and interval 300
```

#### Step 9 Verify the metrics in Asserts

![AWS Lambda Metric in Asserts](<../.gitbook/assets/Screenshot 2021-12-01 at 4.26.51 PM.png>)
