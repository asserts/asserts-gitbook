---
description: Quick start guide for Deploying Asserts on Virtual or Physical Machine
---

# Docker Compose

## **Before you begin**

### **Required P**rerequisites

* [ ] Acquired <mark style="color:blue;">**Asserts License**</mark>
* [ ] At least 4 GB of RAM dedicated to the host running the Docker engine&#x20;
  * 8Gb is recommended
* [ ] <mark style="color:blue;">**Docker Engine**</mark>
* [ ] <mark style="color:blue;">**Docker-Compose**</mark> >= [v1.27.1](https://docs.docker.com/compose/release-notes/#1271)&#x20;

#### **Install Docker Dependencies**

{% tabs %}
{% tab title="Mac" %}
Install Docker Desktop

**Docker Desktop for Mac** includes both **Docker** & **Docker-Compose** in a single installation package.&#x20;



For installation instructions:\
[Install Docker Desktop on Mac](https://docs.docker.com/desktop/mac/install/)
{% endtab %}

{% tab title="Windows" %}
Install Docker Desktop

**Docker Desktop for Windows** includes both **Docker** & **Docker-Compose** in a single installation package.&#x20;



For installation instructions:\
[Install Docker Desktop on Windows](https://docs.docker.com/desktop/windows/install/)
{% endtab %}

{% tab title="Linux" %}
### **Install Docker**

1.  Update the installed packages and package cache on your instance

    ```
    sudo yum update -y
    ```
2.  Install the most recent [Docker Engine](https://docs.docker.com/engine/) package

    ```
    sudo yum install docker
    ```
3.  Start the [Docker](https://docs.docker.com/get-started/overview/) service

    ```
    sudo service docker start
    ```
4.  Verify that you can execute can execute [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) commands

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


{% endtab %}
{% endtabs %}



### **Optional P**rerequisites

All Asserts services publish their own metrics, which are in turn consumed by Asserts so it can monitor itself. This enables you to install and run Asserts without the following prerequisites to get a taste of the value that Asserts aims to provide. However, to realize the full potential that Asserts can provide please consider satisfying the following prerequisites.

*   [ ] A Prometheus compatible endpoint to query (can be multiple)\
    :warning: - _Asserts can not provide actionable insights into your **Prometheus environment** without the ability_ query it's endpoint. __ \
    __

    * [ ] [kube-state-metrics](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-state-metrics) (metrics within the Prometheus endpoint)\
      :warning: - _Asserts can not provide actionable insights on **the state of the objects** managed by your **Cluster** without these metrics._\
      __
    * [ ] [node-exporter](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-node-exporter) (metrics within the Prometheus endpoint)\
      :warning: - _Asserts can not provide insights on_ **Node** (Instance) level resource utilization, performance and health _without these metrics._

    __

## **Install**

1. **Download** the latest Asserts docker-compose files:
   * [Docker-Compose.yml](https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/docker-compose.yml)
   * [.env](https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/.env)
2.  **Pull Latest Images**

    ```
    docker-compose pull
    ```

    ****
3.  **Run Asserts Services**

    ```
    docker-compose up -d
    ```


4.  Open Asserts

    * [Show All Services](http://localhost/entities?id=5051684514890205462\&score=1\&definitionId=15\&boundDescription=Show%20all%20Services\&description=Show%20all%20entity%20type\&bindings%5BentityType%5D=Service\&bindings%5Bupdated%5D=1637190133778\&start=now-15m\&end=now)

    or run

    ```
    open http://localhost
    ```



## Upgrade

1. Download the latest docker files:
   * [Docker-Compose.yml](https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/docker-compose.yml)
   * [.env](https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/.env)
2.  **Pull Latest Images**

    ```
    docker-compose pull
    ```
3.  **Run Asserts Services**

    ```
    docker-compose up -d
    ```



## Uninstall

1.  **Stop Asserts**\
    To Stop and Remove containers:

    ```
    docker-compose down
    ```


2.  **Cleanup Persistent Data**\
    ****Docker data volume persists even after a container is deleted.\
    \
    a. **Remove All Volumes:**

    ```
    docker volume prune
    ```

    \
    b. **Remove one or more volumes:**

    ```
    docker volume rm VOLUME [VOLUME...]
    ```

    ****\
    **VOLUMES:**

    * asserts\_config
    * asserts\_prom-rules
    * asserts\_relabel-rules
    * asserts\_tsdb
    * asserts\_rdb
    * asserts\_graph
    * asserts\_search
    * asserts\_alertmanager



## [Need Help](../#before-you-begin)

