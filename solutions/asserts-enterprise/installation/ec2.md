---
description: Quick start guide for Deploying Asserts on a EC2 Instance
---

# EC2

## **Setup**

### **Install Docker**&#x20;

1.  Launch an instance with the <mark style="color:orange;">Amazon Linux</mark> or <mark style="color:orange;">Amazon Linux 2</mark> AMI.&#x20;

    {% hint style="info" %}
    For more details see 👉 [Launching an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html)&#x20;
    {% endhint %}


2. Connect to your new EC2 instance
3.  Update the installed packages and package cache on your instance

    ```
    sudo yum update -y
    ```
4.  Install the most recent [Docker Engine](https://docs.docker.com/engine/) package

    *   <mark style="color:orange;">Amazon Linux</mark>

        ```
        sudo yum install docker
        ```



    *   <mark style="color:orange;">Amazon Linux 2</mark>

        ```
        sudo amazon-linux-extras install docker
        ```


5.  Start the [Docker](https://docs.docker.com/get-started/overview/) service

    ```
    sudo service docker start
    ```


6.  Add the <mark style="color:green;">**`ec2-user`**</mark> to the <mark style="color:green;">**`docker`**</mark> group so you can execute [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) commands without <mark style="color:red;">**`sudo`**</mark> permissions

    ```
    sudo usermod -a -G docker ec2-user
    ```


7. **Reboot** your instance to ensure the new <mark style="color:green;">**`ec2-user`**</mark> permissions to access the Docker daemon are applied.
8.  Once your new EC2 instance comes back up, re-connect and verify that the <mark style="color:green;">**`ec2-user`**</mark> can execute [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) commands without <mark style="color:red;">**`sudo`**</mark> permissions

    ```bash
    docker -v
    # Docker version 20.10.11, build dea9396
    ```



### Install Docker-Compose

1.  Run this command to download the current stable release of Docker Compose:

    ```bash
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    ```


2.  Apply executable permissions to the binary:

    ```
    sudo chmod +x /usr/local/bin/docker-compose
    ```


3.  Add to Path

    ```
    sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
    ```


4.  Verify that you can execute [docker-compose CLI](https://docs.docker.com/compose/reference/) commands

    ```bash
    docker-compose --version
    # docker-compose version 1.29.2, build 5becea4c
    ```





    {% hint style="info" %}
    For more info see 👉 [Install Docker Compose](https://docs.docker.com/compose/install/)
    {% endhint %}



### Login to Docker

{% hint style="warning" %}
#### Required for Accessing Private Images

To access our private docker images, make sure you login or have configured pull credentials with the [DockerID](https://docs.docker.com/docker-id/)  you've provided us as part of the Enterprise <mark style="color:red;">beta</mark> signup process.
{% endhint %}

1. Login to Docker Hub registry

```bash
docker login
# Authenticating with existing credentials...
# Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
# Username (foo): foo
# Password: _
```

{% hint style="warning" %}
When you use the `docker login` command, your credentials are stored in your home directory in _<mark style="color:green;"><mark style="color:blue;">`.docker/config.json`<mark style="color:blue;"></mark>_` ``and your` password is base64-encoded in this file.
{% endhint %}

We recommend using one of the [Docker credential helpers](https://github.com/docker/docker-credential-helpers) for secure storage of passwords.&#x20;

For extra security, you can also use a [personal access token](https://docs.docker.com/docker-hub/access-tokens/) to login instead, which is still encoded in this file (without a Docker credential helper) but doesn’t allow admin actions (such as changing the password).

{% hint style="info" %}
For more info see 👉 [docker login](https://docs.docker.com/engine/reference/commandline/login/)
{% endhint %}

## **Get Started**

### Create Installation Files

1. Create a  <mark style="color:blue;">`.env`</mark> file with the following contents:

<details>

<summary>Environment File</summary>

{% code title=".env" %}
```bash
COMPOSE_PROJECT_NAME=Asserts

# Authentication Token for License Server
ASSERTS_LICENSE_AUTH_TOKEN=<Your-Asserts-License-Auth-Token>
# Product Id for License
ASSERTS_LICENSE_PRODUCT_ID=<Your-Asserts-License-Product-ID>
# License Key
ASSERTS_LICENSE_KEY=<Your-Asserts-License-Key>
```
{% endcode %}

</details>

2\. Update the values in your <mark style="color:blue;">`.env`</mark> file with your license info

3\. Create a  <mark style="color:blue;">`.docker-compose.yml`</mark> file with the following contents:

<details>

<summary>Docker Compose File</summary>

{% code title="docker-compose.yml" %}
```yaml
version: "3.9"

volumes:
  config:
  prom-rules:
  relabel-rules:
  alertmanager:
  tsdb:
  rdb:
  graph:
  search:
  fluentd:

services:
  jarvis:
    image: asserts/jarvis.dev:v0.1.876
    container_name: jarvis
    restart: on-failure
    depends_on:
      - "authorization"
      - "api-server"
      - "fluentd"
    ports:
      - 80:80
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        tag: jarvis

  api-server:
    image: asserts/api-server.dev:v0.2.12
    container_name: api-server
    restart: on-failure
    depends_on:
      - "authorization"
      - "postgres"
      - "redisearch"
      - "redisgraph"
      - "fluentd"
    ports:
      - 8030:8030
    volumes:
      - config:/opt/asserts/config
      - prom-rules:/opt/asserts/prom-rules
      - relabel-rules:/opt/asserts/relabel-rules
    environment:
      - ASSERTS_LICENSE_AUTH_TOKEN=$ASSERTS_LICENSE_AUTH_TOKEN
      - ASSERTS_LICENSE_PRODUCT_ID=$ASSERTS_LICENSE_PRODUCT_ID
      - ASSERTS_LICENSE_KEY=$ASSERTS_LICENSE_KEY
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        tag: api-server

  assertion-detector:
    image: asserts/assertion-detector.dev:v0.2.12
    container_name: assertion-detector
    restart: on-failure
    depends_on:
      - "postgres"
      - "redisgraph"
      - "fluentd"
    ports:
      - 8040:8040
    volumes:
      - config:/opt/asserts/config
    environment:
      - ASSERTS_LICENSE_AUTH_TOKEN=$ASSERTS_LICENSE_AUTH_TOKEN
      - ASSERTS_LICENSE_PRODUCT_ID=$ASSERTS_LICENSE_PRODUCT_ID
      - ASSERTS_LICENSE_KEY=$ASSERTS_LICENSE_KEY
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        tag: assertion-detector

  model-builder:
    image: asserts/model-builder.dev:v0.2.12
    container_name: model-builder
    restart: on-failure
    depends_on:
      - "postgres"
      - "redisearch"
      - "redisgraph"
      - "fluentd"
    ports:
      - 8060:8060
    volumes:
      - config:/opt/asserts/config
    environment:
      - ASSERTS_LICENSE_AUTH_TOKEN=$ASSERTS_LICENSE_AUTH_TOKEN
      - ASSERTS_LICENSE_PRODUCT_ID=$ASSERTS_LICENSE_PRODUCT_ID
      - ASSERTS_LICENSE_KEY=$ASSERTS_LICENSE_KEY
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        tag: model-builder

  authorization:
    image: asserts/authorization.dev:v0.2.12
    container_name: authorization
    restart: on-failure
    depends_on:
      - "postgres"
      - "fluentd"
    ports:
      - 8070:8070
    volumes:
      - config:/opt/asserts/config
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        tag: authorization
    environment:
      - ASSERTS_LICENSE_AUTH_TOKEN=$ASSERTS_LICENSE_AUTH_TOKEN
      - ASSERTS_LICENSE_PRODUCT_ID=$ASSERTS_LICENSE_PRODUCT_ID
      - ASSERTS_LICENSE_KEY=$ASSERTS_LICENSE_KEY

  registration:
    image: asserts/registration.dev:v0.2.12
    container_name: registration
    restart: on-failure
    depends_on:
      - "postgres"
      - "fluentd"
    ports:
      - 8050:8050
    volumes:
      - config:/opt/asserts/config
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        tag: registration
    environment:
      - ASSERTS_LICENSE_AUTH_TOKEN=$ASSERTS_LICENSE_AUTH_TOKEN
      - ASSERTS_LICENSE_PRODUCT_ID=$ASSERTS_LICENSE_PRODUCT_ID
      - ASSERTS_LICENSE_KEY=$ASSERTS_LICENSE_KEY

  redisearch-exporter:
    image: asserts/redisearch-exporter.dev:v1.0.1
    container_name: redisearch-exporter
    restart: on-failure
    ports:
      - 9121

  redisgraph-exporter:
    image: asserts/redisgraph-exporter.dev:v1.0.1
    container_name: redisgraph-exporter
    restart: on-failure
    ports:
      - 9121

  postgres-exporter:
    image: asserts/postgres-exporter.dev:v1.0.0
    container_name: postgres-exporter
    restart: on-failure
    ports:
      - 9187

  grafana:
    image: asserts/grafana.dev:v1.0.58
    container_name: grafana
    ports:
      - 3000:3000
    restart: always
    depends_on:
      - "fluentd"
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        tag: grafana

  alertmanager:
    image:  asserts/alertmanager.dev:v1.0.0
    container_name: alertmanager
    restart: always
    depends_on:
      - "assertion-detector"
      - "fluentd"
    ports:
      - 9093:9093
    volumes:
      - alertmanager:/alertmanager:rw

    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        tag: alertmanager

  vmalert:
    image: asserts/vmalert.dev:v1.0.2
    container_name: vmalert
    restart: always
    depends_on:
      - "victoriametrics"
      - "alertmanager"
      - "fluentd"
    ports:
      - 8880:8880
    volumes:
      - prom-rules:/etc/asserts:ro

    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        tag: vmalert

  victoriametrics:
    image: asserts/victoria-metrics.dev:v1.0.5
    container_name: victoriametrics
    restart: always
    depends_on:
      - "fluentd"
    ports:
      - 8428:8428
    volumes:
      - tsdb:/storage:rw
      - relabel-rules:/etc/asserts:ro
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        tag: victoria-metrics

  postgres:
    image: asserts/postgres.dev:v1.0.0
    container_name: postgres
    restart: on-failure
    ports:
      - 5432
    volumes:
      - rdb:/var/lib/postgresql/data:rw

  redisearch:
    image: asserts/redisearch.dev:v1.0.1
    container_name: redisearch
    restart: on-failure
    volumes:
      - search:/data:rw
    ports:
      - 6379

  redisgraph:
    image: asserts/redisgraph.dev:v1.0.1
    container_name: redisgraph
    restart: on-failure
    volumes:
      - graph:/data:rw
    ports:
      - 6379

  fluentd:
    image: asserts/fluentd.dev:v1.0.3
    container_name: fluentd
    restart: on-failure
    ports:
      - "24224:24224"
      - "24224:24224/udp"
    volumes:
      - fluentd:/fluentd/log

  collect-logs:
    image: asserts/collect-logs.dev:v1.0.3
    volumes:
      - fluentd:/fluentd/log
      - ./:/logs
    profiles:
      - tools

```
{% endcode %}

</details>

### Start Asserts

1.  **Run Asserts Services**

    ```
    docker-compose up -d
    ```


2.  Open Asserts

    * [Show All Services](http://localhost/entities?id=5051684514890205462\&score=1\&definitionId=15\&boundDescription=Show%20all%20Services\&description=Show%20all%20entity%20type\&bindings%5BentityType%5D=Service\&bindings%5Bupdated%5D=1637190133778\&start=now-15m\&end=now)

    or run

    ```
    open http://localhost
    ```


3. If No issues, Proceed to [Integrate Prometheus](../integrate-prometheus.md)

## **Operation**&#x20;

### **Upgrade Asserts**

1. Update <mark style="color:blue;">`.env`</mark> & <mark style="color:blue;">`docker-compose.yml`</mark> with latest content from [Create Installation Files](ec2.md#create-installation-files) step above 👆
2.  **Re-run docker-compose up**&#x20;

    ```
    docker-compose up -d
    ```

### Stop Asserts

To Stop and Remove containers:

```
docker-compose down
```

### Cleanup

**Persistent Data**

Docker data volume persists even after a container is deleted.

**Remove one or more volumes:**

```
docker volume rm VOLUME [VOLUME...]
```

**VOLUMES:**

* asserts\_config
* asserts\_prom-rules
* asserts\_relabel-rules
* asserts\_tsdb
* asserts\_rdb
* asserts\_graph
* asserts\_search
* asserts\_alertmanager

**Remove All Volumes:**

```
docker volume prune
```

## Troubleshooting&#x20;

### Collect Logs

To collect container logs from Asserts services for troubleshooting run the following:

```
docker-compose run --rm collect-logs
```

It should create a `asserts-logs.tar.gz` file in the root of the repo

**Uncompress the logs tar**

```
tar -xvf asserts-logs.tar.gz
```

The logs will be extracted to:\
📒 working dir

> 📂 fluentd
>
> > 📂 log
> >
> > > 📄 api-server.log\
> > > 📄 assertion-detector.log\
> > > 📄 authorization.log\
> > > 📄 jarvis.log\
> > > 📄 model-builder.log\
> > > 📄 registration.log
>
> 🐳 docker-compose.yml

### Get Help

General support for Asserts is provided via our in app messenger.\
Please reach out with any issues, questions, or even feature requests at any time.\
Simply click on the <img src="../../../.gitbook/assets/intercom-icon-27.jpg" alt="" data-size="line"> icon in the bottom right corner of this page to open our messenger app and <mark style="color:blue;">Send us a message</mark>.
