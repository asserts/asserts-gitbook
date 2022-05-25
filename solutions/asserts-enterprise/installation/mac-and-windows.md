# Mac & Windows

## Set Up

### Install Docker Desktop

**Docker Desktop for Mac & Docker Desktop for Windows** includes both Docker & docker-compose in a single installation package.&#x20;

For installation instructions:

{% tabs %}
{% tab title="Mac" %}
[Install Docker Desktop on Mac](https://docs.docker.com/desktop/mac/install/)
{% endtab %}

{% tab title="Windows" %}
[Install Docker Desktop on Windows](https://docs.docker.com/desktop/windows/install/)
{% endtab %}
{% endtabs %}

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

****

## **Get Started**

### Start Asserts

1.  **Pull Latest Images**

    ```
    docker-compose pull
    ```

    ****
2.  **Run Asserts Services**

    ```
    docker-compose up -d
    ```


3.  Open Asserts

    * [Show All Services](http://localhost/entities?id=5051684514890205462\&score=1\&definitionId=15\&boundDescription=Show%20all%20Services\&description=Show%20all%20entity%20type\&bindings%5BentityType%5D=Service\&bindings%5Bupdated%5D=1637190133778\&start=now-15m\&end=now)

    or run

    ```
    open http://localhost
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
