---
description: Quick start guide for Deploying Asserts on a Kubernetes Cluster
---

# Helm Chart

{% hint style="info" %}
Helm charts and source code can be found in our public [github helm-charts repo.](https://github.com/asserts/helm-charts/tree/master/charts/asserts)
{% endhint %}

## **Prerequisites**

* Kubernetes 1.17+
* Helm 3.2.0+
* PV provisioner support in the underlying infrastructure
* A Prometheus compatible endpoint with [kube-state-metrics](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-state-metrics) and [node-exporter](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-node-exporter)

## **Installing the Chart**

```
helm repo add asserts https://asserts.github.io/helm-charts
helm repo update
helm upgrade --install asserts asserts/asserts -n asserts --create-namespace
```

There any many configuration options such as PagerDuty and Slack integrations. These can be configured with a values file. View all install configuration options [**here**](https://github.com/asserts/helm-charts/blob/master/charts/asserts/values.yaml).

## **Verify and Access**

When Asserts is spinning up for the first time, it usually takes about 3-4 minutes but could take longer depending on the hardware resources allocated (e.g. a kind/k3d cluster).

Once all containers are initialized and running:

```
kubectl get pods -l app.kubernetes.io/instance=asserts -n asserts
```

You can then login to the asserts-ui by running:

```bash
kubectl port-forward -n asserts deployment/asserts-ui 8080 -n asserts
```

And opening your browser to [**http://localhost:8080**](http://localhost:8080/), where you will be directed to the Asserts Registration page. There you can acquire a license.

## **Acquire a License**

You can now view the deployed frontend!

Once there, you can request a trial license.

<figure><img src="../../.gitbook/assets/Asserts Request Trial License.gif" alt=""><figcaption></figcaption></figure>

## Configuring a Prometheus DataSource

Configure your Prometheus DataSource which Asserts will connect to and query by following these [**instructions**](https://docs.asserts.ai/integrations/data-source/prometheus). Once you're connected, you can see the data and start observing!

## Upgrading the Chart

```
helm repo update
helm upgrade --install asserts asserts/asserts -n asserts
```

## Uninstalling the Chart

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

