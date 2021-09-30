# Getting Started

Asserts is cloud-agnostic and has no proprietary application agents to install. It is natively built on the open [Prometheus](https://prometheus.io/) ecosystem with 100s of [exporters](https://prometheus.io/docs/instrumenting/exporters/).

You can get started by adding _remote-write_ to Asserts cloud in your _prometheus.yml_

![](../.gitbook/assets/4.png)

### **Remote Write**

Here’s an example of Prometheus remote write configuration:

```yaml
global:
  external_labels:
    asserts_tenant: your-tenant-name
    
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
- source_labels: [namespace] # keep all metrics in the dev namespace
  regex: dev
  target_label: __tmp_keep
  replacement: 'true'
- source_labels: [created_by_kind] # drop all metrics with label name -> created_by_kind=Job
  regex: Job
  action: drop
- source_labels: [__name__] # keep all metrics matching the various metrics names (this will include those outside the dev namespace)
  regex: node_.*|kubelet_.*|kube_node_.*|prometheus_.*|alertmanager_.*
  target_label: __tmp_keep
  replacement: 'true'
- source_labels: [__tmp_keep] # keep all the metrics with the label __tmp_keep=true
  regex: 'true'
  action: keep
- regex: __tmp_keep # drop all the __tmp_keep labels (not the metric)
  action: labeldrop
  queue_config:
  capacity: 10000
  max_samples_per_send: 10000
  max_shards: 10
```

