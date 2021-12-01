# Getting Started

Asserts is cloud-agnostic and has no proprietary application agents to install. It is natively built on the open [Prometheus](https://prometheus.io) ecosystem with 100s of [exporters](https://prometheus.io/docs/instrumenting/exporters/).

You can get started by adding _remote-write_ to Asserts cloud in your _prometheus.yml_

![](../.gitbook/assets/4.png)

### **Remote Write**

Here’s an example of Prometheus remote write configuration:

```yaml
global:
  external_labels:
    asserts_tenant: your-tenant-name # required
    asserts_env: your-env # recommended dev/stage/prod or k8s cluster name
    asserts_site: your-site # optional us-west-2 / us-east-1

remote_write:
  - url: https://your-tenant-name.tsdb.asserts.ai/insert/0/prometheus/
    basic_auth:
      username: your-tenant-name
      password: <secret>
    queue_config:
      capacity: 10000
      max_samples_per_send: 10000
      max_shards: 10
```

Asserts will provide your tenant name and password used in the above configuration during onboarding.

#### Filtering with Relabeling

Sometimes it’s desirable to only remote write some metrics. You can add a write\_relabel\_configs section to do this:

```yaml
remote_write:
  - url: https://your-tenant-name.tsdb.asserts.ai/insert/0/prometheus/
    basic_auth:
      username: your-tenant-name
      password: <secret>
    write_relabel_configs:
    - source_labels: [ namespace ] # keep all metrics in the dev namespace
      regex: dev
      target_label: __tmp_keep
      replacement: 'true'
    - source_labels: [ created_by_kind ] # drop all metrics with label name -> created_by_kind=Job
      regex: Job
      action: drop
    - source_labels: [ __name__ ] # keep all metrics matching the various metrics names (this will include those outside the dev namespace)
      regex: node_.*|kubelet_.*|kube_node_.*|prometheus_.*|alertmanager_.*
      target_label: __tmp_keep
      replacement: 'true'
    - source_labels: [ __tmp_keep ] # keep all the metrics with the label __tmp_keep=true
      regex: 'true'
      action: keep
    - regex: __tmp_keep # drop all the __tmp_keep labels (not the metric)
      action: labeldrop
    queue_config:
      capacity: 10000
      max_samples_per_send: 10000
      max_shards: 10
```

### CloudWatch exporter setup for AWS Serverless Monitoring

To monitor AWS Lambda or Fargate, the [AWS CloudWatch exporter](https://github.com/asserts/aws-cloudwatch-exporter) needs to be set up. This exporter retrieves CloudWatch metrics and AWS configurations, exports them in Prometheus metric format and ingests them into Asserts cloud. Asserts provides a bundle that includes the AWS exporter and [vmagent](https://docs.victoriametrics.com/vmagent.html) to remote-write metrics into Asserts Cloud.&#x20;

#### AWS CloudWatch Exporter EC2 Installation

Create a stack using [the CloudFormation template](https://github.com/asserts/aws-cloudwatch-exporter/blob/main/AWSCloudFormationTemplate.json) to set up the necessary IAM policy, role and EC2 instance profile

```bash
git clone git@github.com:asserts/aws-cloudwatch-exporter.git
cd aws-cloudwatch-exporter
aws cloudformation create-stack \
    --stack-name asserts-aws-exporter \
    --template-body file://$PWD/AWSCloudFormationTemplate.json \
    --capabilities CAPABILITY_NAMED_IAM
```

Create a `t2.micro` EC2 instance and attach the IAM Instance profile `asserts-aws-exporter-role` to it

Download the [Asserts AWS exporter zip](https://github.com/asserts/aws-cloudwatch-exporter/releases/download/v1.0.90/aws-cloudwatch-exporter.tar.gz) on this EC2 and extract it

```shell
curl 'https://objects.githubusercontent.com/github-production-release-asset-2e65be/413873030/1da738cd-9e53-47b2-aed8-fc0faebdd0f9?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20211201%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20211201T064414Z&X-Amz-Expires=300&X-Amz-Signature=9ec66b479b5df98ba6c24e7d99735da4011af1c0bef7954989e50d08221c5f92&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=413873030&response-content-disposition=attachment%3B%20filename%3Daws-cloudwatch-exporter.tar.gz&response-content-type=application%2Foctet-stream' \
  --compressed --output aws-exporter.tar.gz
tar -xf aws-cloudwatch-exporter.tar.gz
cd ansible
ls -al
total 16
drwxr-xr-x  6 user  staff   192 Nov 29 16:36 .
drwxr-xr-x  3 user  staff    96 Nov 30 17:09 ..
drwxr-xr-x  9 user  staff   288 Nov 29 16:36 aws-cloudwatch-exporter-1.0.90
-rw-r--r--  1 user  staff  1395 Nov 29 16:35 configure-services.yaml
-rw-r--r--  1 user  staff   153 Nov 15 19:36 install.sh
drwxr-xr-x  4 user  staff   128 Nov 15 19:35 roles
```

The `configure-services.yaml` should look like this

```yaml
---
- name: deploy cloudwatch exporter
  hosts: "localhost"
  become: true
  become_user: root
  vars:
    hosts: localhost
  pre_tasks:
  roles:
    - role: ansible-role-java
      java_packages:
        - openjdk-8-jdk
    - role: ansible-vmagent
      vmagent_remote_write:
        - url: <REMOTE-WRITE-URL> -remoteWrite.basicAuth.username=<USERNAME> -remoteWrite.basicAuth.password=<PASSWORD>
      vmagent_global:
        scrape_interval: 60s
        scrape_timeout: 30s
        external_labels:
          asserts_tenant: <ASSERTS-TENANT>
          asserts_env: <ENVIRONMENT-NAME>
      vmagent_scrape_configs:
        - job_name: vmagent
          static_configs:
          - targets:
            - localhost:8429
        - job_name: aws-exporter
          metrics_path: '/aws-exporter/actuator/prometheus'
          static_configs:
          - targets:
            - localhost:8010
          metricRelabelings:
          - sourceLabels: [__name__, region]
            regex: aws_.+;(.+)
            targetLabel: asserts_site
            action: replace
            replacement: $1

  post_tasks:
    - name: Run the exporter
      shell: |
        cd AWS_EXPORTER_DIRECTORY_NAME/
        cp conf/cloudwatch_scrape_config_sample.yml .
        mv cloudwatch_scrape_config_sample.yml cloudwatch_scrape_config.yml
        bin/aws-cloudwatch-exporter &
        exit 0
```

Set the values for the following placeholders&#x20;

1. `REMOTE-WRITE-URL` Your Asserts remote write URL. For e.g. `https://acme.tsdb.asserts.ai/insert/0/prometheus/`
2. `USERNAME/PASSWORD` Your Asserts tenant username and password
3. `TENANT-NAME` Your Asserts tenant name

### AWS Region

The AWS region will have to be set in `aws-cloudwatch-exporter-1.0.90/cloudwatch-scrape-configuration.yml`

The default value would look like

```
regions:
  - us-west-2
```

Specify the region(s) that need to be scraped

### Environment configuration

`external_labels` Specify your environment name here if the environment cannot be identified from the metric. This is also a simpler option to get started with

`metricRelabelings` Use this option if the environment information is available in one of the metric labels. For e.g., if your environment name is present in a tag named `env_name` on your AWS Lambda functions or ECS Services, the metric will have a label called `tag_env_name`. You could then specify the environment as follows&#x20;

```
          # __name__ will have the metric name. For e.g. aws_lambda_invocations_sum
          - sourceLabels: [__name__, tag_env_name]
            regex: aws_.+;(.+)
            targetLabel: asserts_env
            action: replace
            replacement: $1 
```

&#x20;Start the services by running the following command

```
bash -x install.sh
```

Check the `aws-exporter.log` to see if the exporter has started scraping metrics. The following log entries should be present&#x20;

```
INFO  2021-11-30 16:55:40.442 main StartupInfoLogger Started CloudWatchExporterApplication in 7.251 seconds (JVM running for 7.626)
...
INFO  2021-11-30 16:55:45.449 pool-1-thread-5 MetricScrapeTask BEGIN Scrape for region us-west-2 and interval 300
...
INFO  2021-11-30 16:55:57.088 pool-1-thread-5 MetricScrapeTask END Scrape for region us-west-2 and interval 300
```

You can log into Asserts and verify that your metrics are being ingested.&#x20;

![](<../.gitbook/assets/image (6).png>)

The default scrape interval is set to `300` seconds. For more information on configuring the exporter, check [the documentation](https://github.com/asserts/aws-cloudwatch-exporter)&#x20;
