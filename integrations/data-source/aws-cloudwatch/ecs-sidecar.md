# ECS Sidecar

#### Installing the Asserts ECS sidecar container&#x20;

The ECS sidecar container needs to be included in the task definition of each ECS Task. Here is a sample JSON excerpt of the sidecar container definition that can be added to your existing task definition JSON.

```json
{
  "taskDefinitionArn": "arn:aws:ecs:us-west-2:123123123:task-definition/hello-world:1",
  "containerDefinitions": [
    {
    ...
    },
    {
      "name": "ecs-dockerstats-exporter",
      "image": "asserts/ecs-dockerstats-exporter:latest-release",
      "portMappings": [
        {
          "containerPort": 8014,
          "hostPort": 8014,
          "protocol": "tcp"
        }
      ],
      "essential": false,
      "dependsOn": [
        {
          "containerName": "cloudwatch-exporter",
          "condition": "START"
        }
      ],
      "dockerLabels": {
        "PROMETHEUS_EXPORTER_PATH": "/container-stats/actuator/prometheus",
        "PROMETHEUS_EXPORTER_PORT": "8014"
      },
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "<LOG-GROUP>",
          "awslogs-region": "us-west-2",
          "awslogs-stream-prefix": "ecs-dockerstats-exporter"
        }
      },
      "environment": [
        {
          "name": "remoteWrite_basicAuth_username",
          "value": "chief"
        },
        {
          "name": "remoteWrite_url",
          "value": "https://chief.tsdb.dev.asserts.ai/api/v1/write"
        },
        {
          "name": "SKIP_TLS_VERIFY",
          "value": "true"
        }
      ],
      "secrets": [
        {
          "name": "remoteWrite_basicAuth_password",
          "valueFrom": "arn:aws:secretsmanager:us-west-2:123123123:secret:AssertsRemoteWriteCredentials-Dev-wNNbN0:password::"
        }
      ]
    }
  ]
  ...
}
```

The ECS Sidecar can operate in two modes

#### Active Mode

In the `Active` mode, the sidecar will gather container level metrics for CPU, Memory and Network bytes and remote write the metrics to a Prometheus TSDB. In the above example, the remote write URL and credentials for the Prometheus TSDB are provided. The `Active` mode requires that the ECS Task IAM role should have the following permissions

```
    {
        "Effect": "Allow",
        "Action": [
            "ec2:DescribeSubnets",
            "ecs:DescribeTasks",
            "ecs:DescribeTaskDefinition"
        ],
        "Resource": "*"
    }
```

&#x20;

#### Passive Mode

In the `Passive` mode, the sidecar will gather container level metrics for CPU, Memory and Network bytes. But it will need to be scraped by the AWS Exporter.&#x20;
