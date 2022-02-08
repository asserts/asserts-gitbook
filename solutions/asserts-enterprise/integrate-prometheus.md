---
description: Configure your Prometheus remote write integration
---

# Integrate Prometheus

The **remote write** feature of Prometheus enables it to send samples to external storage.&#x20;

You can get Prometheus data flowing into Asserts with a few simple steps.

1. Update your Prometheus server config file
2. (Optional) - Filtering with Relabeling
3. Trigger the reload endpoint for your Prometheus server



## Update Config

### Remote Write

You will need to add (or modify) a prometheus [<mark style="color:blue;">`remote-write`</mark>](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#remote\_write) configuration on your Prometheus server in order to send metrics to the Asserts Enterprise remote write url endpoint. A good example prometheus configuration file can be found [here.](https://github.com/prometheus/prometheus/blob/main/config/testdata/conf.good.yml)

The configuration of your Prometheus server depends on your installation. In general, you configure the <mark style="color:blue;">`remote_write`</mark> section in the <mark style="color:blue;">`prometheus.yml`</mark> configuration file like so:

```yaml
global:
...
  # Existing Configuration
...

remote_write:
  - url: http://<Asserts-Enterprise-Url>:8428/api/v1/write
    queue_config:
      capacity: 10000
      max_samples_per_send: 10000
      max_shards: 10
```

### Authorization

The remote write <mark style="color:blue;">`url`</mark> for Asserts Enterprise does not enforce Auth out of the box.\
Once you're confident that Asserts Enterprise is the right fit for your team, we can work with you to ensure the security of this endpoint meets or exceeds your needs.

### Filtering with Relabeling

Sometimes it’s desirable to only remote write some metrics. \
You can add a <mark style="color:blue;">`write_relabel_configs`</mark> section to do this:

```yaml
remote_write:
  - url: http://<Asserts-Enterprise-Url>:8428/api/v1/write
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

## Reload Config

After updating the configuration file, you will either need to restart the prometheus server or trigger the reload endpoint. This endpoint can only be triggered if the prometheus server was started with `--web.enable-lifecycle` flag. The endpoint can be hit by running:

```bash
curl -X POST http://<prometheus server url>/-/reload
```

