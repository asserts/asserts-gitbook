---
description: Quick start guide for Deploying Asserts on a Kubernetes Cluster
---

# Helm Chart

{% hint style="info" %}
Helm charts and source code can be found in our public [github helm-charts repo.](https://github.com/asserts/helm-charts/tree/master/charts/asserts)
{% endhint %}

## **Architecture**

<figure><img src="../../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>

## [**Join Our Slack**](https://join.slack.com/t/asserts-community/shared\_invite/zt-1qxudnxgl-h3TbJs3HDk4Pk4ssEDu\~1Q)

## **Prerequisites**

* Kubernetes 1.17+
* Helm 3.2.0+
* PV provisioner support in the underlying infrastructure
* A Prometheus compatible endpoint with [kube-state-metrics](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-state-metrics) and [node-exporter](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-node-exporter)

## **Installing the Chart**

```
helm repo add asserts https://asserts.github.io/helm-charts
helm repo update
helm upgrade asserts asserts/asserts \
    --install \
    --namespace asserts \
    --create-namespace
```

#### Enabling AWS Integration

AWS Integration is enabled by activating the [AWS Exporter](../../integrations/data-source/aws-cloudwatch/aws-exporter.md). This is done by setting the helm value `server.awsExporterEnabled` to `true`. For e.g.&#x20;

```
helm repo add asserts https://asserts.github.io/helm-charts
helm repo update
helm upgrade asserts asserts/asserts \
    --install \
    --namespace asserts \
    --create-namespace
    -f values.yaml
```

where `values.yaml` looks like

```
server:
    awsExporterEnabled: true
```

Please note that for AWS Integration to work, the nodes on which Asserts is installed must have some AWS IAM role assigned. Enabling the AWS exporter does not trigger any AWS processing yet. For Asserts to observe an AWS Account, you will need to [add the AWS Account](../../integrations/data-source/aws/add-account.md)&#x20;

#### eBPF Probe

Asserts uses metric label information to build the Entity Graph; it will use labels from service meshes (Istio, Linkerd) or from the Asserts eBPF Probe. By default, the probe is not enabled; it can be enabled via a values file or from the Helm command line.

```bash
helm upgrade asserts asserts/asserts \
    --upgrade \
    --namespace asserts \
    --create-namespace \
    --set ebpfProbe.enabled=true
```

There any many **other configuration** options, such as PagerDuty and Slack integrations. These can be configured with a values file. View all install configuration options [**here**](https://github.com/asserts/helm-charts/blob/master/charts/asserts/values.yaml).&#x20;

## **Verify and Access**

When Asserts is spinning up for the first time, it usually takes about 3-4 minutes but could take longer depending on the hardware resources allocated (e.g. a kind/k3d cluster).

Once all containers are initialized and running:

```
kubectl get pods -l app.kubernetes.io/instance=asserts -n asserts -w
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

## Sizing Values

The default Helm chart values in the [values.yaml](https://github.com/asserts/helm-charts/blob/master/charts/asserts/values.yaml) file are configured to allow you to quickly get Asserts up and running. Here are some sample values files for sizing resources according to the total number of raw metrics at the source Prometheus endpoints:

[small](https://github.com/asserts/helm-charts/blob/master/charts/asserts/small.yaml): Up to 1 million metrics

[medium](https://github.com/asserts/helm-charts/blob/master/charts/asserts/medium.yaml): 1 to 5 million metrics

[large](https://github.com/asserts/helm-charts/blob/master/charts/asserts/large.yaml): Over 5 million metrics

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

