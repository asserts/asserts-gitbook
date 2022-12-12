---
description: Quick start guide for Deploying Asserts on a Kubernetes Cluster
---

# Helm Chart

{% hint style="info" %}
Helm charts and source code can be found in our public [github helm-charts repo.](https://github.com/asserts/helm-charts/tree/master/charts/asserts)
{% endhint %}

## **Prerequisites**:

* Kubernetes 1.17+
* Helm 3.2.0+
* PV provisioner support in the underlying infrastructure
* A Prometheus compatible endpoint to query
* [kube-state-metrics](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-state-metrics) (metrics from the Prometheus endpoint)
* [node-exporter](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-node-exporter) (metrics from the Prometheus endpoint)

## **Installing the Chart:**

View all install configuration options [**here**](https://github.com/asserts/helm-charts/blob/master/charts/asserts/values.yaml).

#### You ARE running Prometheus-Operator in the same cluster you are installing Asserts

Install the chart and create the asserts namespace

```
helm repo add asserts https://asserts.github.io/helm-charts
helm repo update
helm upgrade --install asserts asserts/asserts -n asserts --create-namespace
```

#### You ARE NOT running Prometheus-Operator in the same cluster as where Asserts is installed

Create a values file, here we will call it `values.yaml`

{% code title="values.yaml" %}
```yaml
## Asserts cluster env and site
##
## IGNORE IF RUNNING Promtheus-Operator!!!
##
## This is required in order for Asserts to scrape a
## and monitor itself. This should be set to the env and site
## of the cluster asserts is being installed in.
## This means that the env and site of the datasource
## for this cluster (set in the Asserts UI after installing)
assertsClusterEnv: <your-env>
# optional (e.g. us-west-2)
assertsClusterSite: ""

# Set since not running Prometheus-Operator (default is true)
serviceMonitor:
  enabled: false
```
{% endcode %}

Install the chart with overrides and create the asserts namespace

```shell
helm repo add asserts https://asserts.github.io/helm-charts
helm repo update
helm upgrade --install asserts asserts/asserts -n asserts -f values.yaml --create-namespace
```

When Asserts is spinning up for the first time, it usually takes about 3-4 minutes but could take longer depending on the hardware resources allocated (e.g. a kind cluster).

## **Verify and Access**

### Verify containers initializing and running

```
kubectl get pods -l app.kubernetes.io/instance=asserts -n asserts
```

### **Enable port-forward to login**

```bash
kubectl port-forward -n asserts deployment/asserts-ui 8080 -n asserts
```

### **Acquire a License**

You can now view the deployed frontend by visiting the following link.

[**http://localhost:8080**](http://localhost:8080)

Once there, you can request a trial license.

<figure><img src="../../.gitbook/assets/Asserts Request Trial License.gif" alt=""><figcaption></figcaption></figure>

### Configuring a Prometheus DataSource and see the data!

Configure your Prometheus DataSource which Asserts will connect to and query by following these [instructions](https://docs.asserts.ai/integrations/data-source/prometheus). Once you're connected, you can see the data and start observing!

## Uninstall

To uninstall Asserts and its dependencies, run the following command:

```bash
helm uninstall asserts -n asserts
```

The previous command removes all the Kubernetes components but PVCs associated with the chart and deletes the release.

To delete the PVCs associated with `asserts`:

```
kubectl delete pvc -l app.kubernetes.io/instance=asserts -n asserts
```

## [Need Help](<../../README (1).md#before-you-begin>)

