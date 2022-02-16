# Getting Started

Asserts is cloud-agnostic and has no proprietary application agents to install. It is natively built on the open [Prometheus](https://prometheus.io) ecosystem with 100s of [exporters](https://prometheus.io/docs/instrumenting/exporters/).

You can start by configuring a _remote-write_ on your Prometheus to write to the Asserts Cloud

![](../.gitbook/assets/4.png)

### **Remote Write**

Remote Writing from Prometheus can be done multiple ways. Here we cover using a standard [Prometheus config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#remote\_write) as well as using the values from the [Prometheus-Operator](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/values.yaml) (kube-prometheus-stack) helm chart.

#### Standard Prometheus Config

Here’s an example of a Prometheus remote write configuration:

```yaml
global:
  external_labels:
    asserts_env: <your-env> # recommended dev/stage/prod or k8s cluster name
    asserts_site: <your-site> # optional us-west-2 / us-east-1

remote_write:
  - url: https://<your-tenant-name>.tsdb.asserts.ai/api/v1/write
    basic_auth:
      username: <your-tenant-name>
      password: <secret>
    queue_config:
      capacity: 10000
      max_samples_per_send: 10000
      max_shards: 10
```

Here you will populate `external_labels` (optional) but ensuring to fill in `<your-tenant-name>` for the remote\_write url and the `basic_auth` username. Additionally, the `<secret>` needs to be filled out with the remote-write password Asserts has provided during onboarding.

Sometimes it’s desirable to only remote write some metrics. You can add a `write_relabel_configs` section to do this:

```yaml
remote_write:
  - url: https://<your-tenant-name>.tsdb.asserts.ai/api/v1/write
    basic_auth:
      username: <your-tenant-name>
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

#### Prometheus Operator Helm Chart Config

First, you'll want to create a [kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/) secret from which to use to authenticate to Asserts via remote-write. Just fill in the `<your-namespace>`, `<your-tenant-name>`, and `<your-password>` blocks, then run the following:

```bash
kubectl create secret generic asserts-remote-write \
  --namespace=<your-namespace> \
  --from-literal=username=<your-tenant-name> \
  --from-literal=password=<your-password>
```

You can verify the secret has been created with the appropriate password by running:

```
kubectl get secret asserts-remote-write -n <namespace> -o jsonpath={.data.password} | base64 --decode
```

Now, configure your prometheus-operator values, filling in the `externalLabels` (optional) but ensuring `<your-tenant-name>` is populated for the remoteWrite url.

```yaml
  prometheus:
    prometheusSpec:
      externalLabels:
        asserts_env: <your-env> # recommended dev/stage/prod or k8s cluster name
        asserts_site: <your-site> # optional us-west-2 / us-east-1
      remoteWrite:
        - url: https://<your-tenant-name>.tsdb.asserts.ai/api/v1/write
          basicAuth:
            password:
              name: asserts-remote-write
              key: password
            username:
              name: asserts-remote-write
              key: username        
          queueConfig:
            capacity: 10000
            maxSamplesPerSend: 10000
            maxShards: 10
```

Reiterating from the standard configuration, Asserts will provide your tenant name and password used in the above configuration during onboarding.

Sometimes it’s desirable to only remote write some metrics. You can add a `writeRelabelConfigs` section to do this:

```yaml
  prometheus:
    prometheusSpec:
      remoteWrite:
        - url: https://<your-tenant-name>.tsdb.asserts.ai/api/v1/write
          basicAuth:
            password:
              name: asserts-remote-write
              key: password
            username:
              name: asserts-remote-write
              key: username      
          writeRelabelConfigs:
            - sourceLabels: [ namespace ] # keep all metrics in the dev namespace
              regex: dev
              targetLabel: __tmp_keep
              replacement: 'true'
            - sourceLabels: [ created_by_kind ] # drop all metrics with label name -> created_by_kind=Job
              regex: Job
              action: drop
            - sourceLabels: [ __name__ ] # keep all metrics matching the various metrics names (this will include those outside the dev namespace)
              regex: node_.*|kubelet_.*|kube_node_.*|prometheus_.*|alertmanager_.*
              targetLabel: __tmp_keep
              replacement: 'true'
            - sourceLabels: [ __tmp_keep ] # keep all the metrics with the label __tmp_keep=true
              regex: 'true'
              action: keep
            - regex: __tmp_keep # drop all the __tmp_keep labels (not the metric)
              action: labeldrop 
          queueConfig:
            capacity: 10000
            maxSamplesPerSend: 10000
            maxShards: 10
```
