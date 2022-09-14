---
description: Quick start guide for Deploying Asserts on Virtual or Physical Machine
---

# Docker Compose

## **Before you begin**

### **Required P**rerequisites

* At least 4 GB of RAM dedicated to the host running the Docker engine&#x20;
  * 8Gb is recommended
* **Docker Engine**
* **Docker-Compose** >= [v1.27.1](https://docs.docker.com/compose/release-notes/#1271)&#x20;

#### **Install Docker Dependencies**

{% tabs %}
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
    export PATH=$PATH:/usr/local/bin
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
{% endtabs %}



### **Optional P**rerequisites

All Asserts services publish their own metrics, which are in turn consumed by Asserts so it can monitor itself. This enables you to install and run Asserts without the following prerequisites to get a taste of the value that Asserts aims to provide. However, to realize the full potential that Asserts can provide please consider satisfying the following prerequisites.

* A Prometheus compatible endpoint to query (can be multiple)
* [node-exporter](https://github.com/prometheus/node\_exporter)
* [cadvisor](https://github.com/google/cadvisor)

__

## **Install**

1. **Download** the latest Asserts docker-compose files:
   * [Docker-Compose.yml](https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/self-hosted-release/docker-compose.yml)
   * [.env](https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/self-hosted-release/.env)
   * [README](https://github.com/asserts/downloads/blob/main/self-hosted-release/README.md)
2.  **Pull Latest Images**

    ```
    docker-compose pull
    ```

    ****
3.  **Run Asserts Services**

    ```
    docker-compose up -d --remove-orphans
    ```


4.  Open Asserts

    * [Show All Services](http://localhost/entities?id=5051684514890205462\&score=1\&definitionId=15\&boundDescription=Show%20all%20Services\&description=Show%20all%20entity%20type\&bindings%5BentityType%5D=Service\&bindings%5Bupdated%5D=1637190133778\&start=now-15m\&end=now)

    or run

    ```
    open http://localhost
    ```



## Upgrade

1. Download the latest docker files:
   * [Docker-Compose.yml](https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/self-hosted-release/docker-compose.yml)
   * [.env](https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/self-hosted-release/.env)
2.  **Pull Latest Images**

    ```
    docker-compose pull
    ```
3.  **Run Asserts Services**

    ```
    docker-compose up -d --remove-orphans
    ```



## Uninstall

1.  **Stop Asserts**\
    To Stop and Remove containers:

    ```
    docker-compose down
    ```


2. **Managing Volumes**\
   ****Docker data volume persists even after a container is deleted. See how to manage persistent data [here](https://docs.docker.com/storage/volumes/)



## [Need Help](../#before-you-begin)

