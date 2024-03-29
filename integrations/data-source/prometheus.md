---
description: Configure Prometheus metric data sources
---

# Prometheus

### Connecting Asserts to your Prometheus

Asserts needs access to your Prometheus endpoint(s) to give you visibility into your systems. You can add multiple Prometheus data sources.

Once you get your trial license, simply select the **Data Sources -> Prometheus** menu and enter your Prometheus connection information:

* **Url:** Prometheus Endpoint Url
* **Env (Required):** The Environment of the Prometheus (e.g. dev/stage/prod)
* **Site (Optional):** The Site (which could be a region/cluster/etc..) of the Prometheus (e.g. us-west-2)
* **Username (Optional):** The User for basic auth
* **Password/Token (Optional):** Password for basic auth or Bearer Token
* **For Rules:** Enabling this slider will result in Asserts' rules getting run against this Prometheus server. Enable this slider when adding a Prometheus server with realtime, fast access metrics\
  (Note: you must have at least one datasource enabled for rules & dashboards)
* **For Dashboards:** Enabling this slider results in this Prometheus server only being utilized for dashboards. Enable this slider for your long term Prometheus storage

Select _**Test Connection**_ to verify your endpoint, then _**Add New.**_ Once you have connected your Prometheus Asserts will discover your service graph within the next minute.

Now navigate to the **Entity Graph** and type **Show all Services** in the the search bar to visualize your services. Learn more about exploring Asserts from our [**user guide**](broken-reference/)**.**

<figure><img src="../../.gitbook/assets/screencast-entity-graph (1).gif" alt=""><figcaption></figcaption></figure>

### Preparing your Prometheus

Asserts leverages the labels in your existing application and infrastructure metrics, such as `pod, node, instance, service, job, namespace et al`.

If you _are_ using [_**Prometheus-Operator**_](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack), these labels are there by default, and _you can_ skip this section

If you are using annotation based [kubernetes service discovery](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#kubernetes\_sd\_config) in your Prometheus configuration such as used in the official [Prometheus Helm Chart default scrape config](https://github.com/prometheus-community/helm-charts/blob/main/charts/prometheus/values.yaml#L771), some **relabel configurations** will need to be added.

Using the mentioned scrape config as our base config we can see the [diff for the required changes](https://github.com/asserts/prometheus-helm-charts/pull/1/files) which results from doing the following 3 steps:

1. Add the **node** label to the jobs **`kubernetes-nodes`** and **`kubernetes-nodes-cadvisor`**:

```
  - source_labels: [__meta_kubernetes_node_name]
    regex: (.+)
    target_label: node
    replacement: $1
```

2\. Add the **pod** label to jobs **`kubernetes-service-endpoints`** and **`kubernetes-service-endpoints-slow`**:

```
  - source_labels: [__meta_kubernetes_pod_name]
    regex: (.+)
    target_label: pod
    replacement: $1
```

3\. Create a **service** label for jobs **`kubernetes-pods`** and **`kubernetes-pods-slow`** from the pod controller name by adding:

```
  - source_labels: [__meta_kubernetes_pod_controller_kind]
    regex: ReplicaSet.*|Job.*
    target_label: __tmp_controller
    replacement: exists
  - source_labels: [__meta_kubernetes_pod_controller_name, __tmp_controller]
    regex: (.*)-(.+);exists
    target_label: service
    replacement: $1
  - source_labels: [__meta_kubernetes_pod_controller_name, __tmp_controller]
    regex: (.+);
    target_label: service
    replacement: $1
```
