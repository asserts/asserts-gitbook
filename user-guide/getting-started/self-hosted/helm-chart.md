---
description: Quick start guide for Deploying Asserts on a Kubernetes Cluster
---

# Helm Chart

## **Before you begin**

### **Required P**rerequisites

In order to deploy the Asserts helm chart, the following prerequisites <mark style="color:red;">**must**</mark> be satisfied :

* [ ] Acquired <mark style="color:blue;">**Asserts License**</mark>
* [ ] Helm client (version 3.0+)\
  [_Install Helm_](https://helm.sh/docs/intro/install/)__
* [ ] Target Cluster is running Kubernetes 1.17+
* [ ] Target Cluster must have PV provisioner support

<details>

<summary><strong></strong><span data-gb-custom-inline data-tag="emoji" data-code="2139">ℹ</span> <strong>- k8s Persistent Volumes</strong></summary>

**PersistentVolume** types are implemented as plugins.\
Kubernetes currently supports the following plugins:

* [`awsElasticBlockStore`](https://kubernetes.io/docs/concepts/storage/volumes/#awselasticblockstore) - AWS Elastic Block Store (EBS)
* [`azureDisk`](https://kubernetes.io/docs/concepts/storage/volumes/#azuredisk) - Azure Disk
* [`azureFile`](https://kubernetes.io/docs/concepts/storage/volumes/#azurefile) - Azure File
* [`cephfs`](https://kubernetes.io/docs/concepts/storage/volumes/#cephfs) - CephFS volume
* [`csi`](https://kubernetes.io/docs/concepts/storage/volumes/#csi) - Container Storage Interface (CSI)
* [`fc`](https://kubernetes.io/docs/concepts/storage/volumes/#fc) - Fibre Channel (FC) storage
* [`gcePersistentDisk`](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk) - GCE Persistent Disk
* [`glusterfs`](https://kubernetes.io/docs/concepts/storage/volumes/#glusterfs) - Glusterfs volume
* [`hostPath`](https://kubernetes.io/docs/concepts/storage/volumes/#hostpath) - HostPath volume
  * &#x20;for single node testing only
  * WILL NOT WORK in a multi-node cluster
  * consider using `local` volume instead
* [`iscsi`](https://kubernetes.io/docs/concepts/storage/volumes/#iscsi) - iSCSI (SCSI over IP) storage
* [`local`](https://kubernetes.io/docs/concepts/storage/volumes/#local) - local storage devices mounted on nodes.
* [`nfs`](https://kubernetes.io/docs/concepts/storage/volumes/#nfs) - Network File System (NFS) storage
* [`portworxVolume`](https://kubernetes.io/docs/concepts/storage/volumes/#portworxvolume) - Portworx volume
* [`rbd`](https://kubernetes.io/docs/concepts/storage/volumes/#rbd) - Rados Block Device (RBD) volume
* [`vsphereVolume`](https://kubernetes.io/docs/concepts/storage/volumes/#vspherevolume) - vSphere VMDK volume

</details>

### **Optional P**rerequisites

All Asserts services publish their own metrics, which are in turn consumed by Asserts so it can monitor itself. This enables you to install and run Asserts without the following prerequisites to get a taste of the value that Asserts aims to provide. However, to realize the full potential that Asserts can provide please consider satisfying the following prerequisites.

* [ ] A Prometheus compatible endpoint to query (can be multiple)\
  :warning: - _Asserts can not provide actionable insights into your **Prometheus environment** without the ability_ query it's endpoint. __ \
  __
  * [ ] [kube-state-metrics](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-state-metrics) (metrics within the Prometheus endpoint)\
    :warning: - _Asserts can not provide actionable insights on **the state of the objects** managed by your **Cluster** without these metrics._\
    __
  * [ ] [node-exporter](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-node-exporter) (metrics within the Prometheus endpoint)\
    :warning: - _Asserts can not provide insights on_ **Node** (Instance) level resource utilization, performance and health _without these metrics._\


****

## **Install**

### **Install Asserts Helm Chart**

#### **1. Add Asserts Chart Repository to local Helm**

```shell
helm repo add asserts https://asserts.github.io/helm-charts
```

#### 2. Set Chart Configuration Overrides

Create a **values.yaml** with chart overrides

{% code title="values.yaml" %}
```yaml
prometheusEndpoints:
  - url: kube-prometheus-stack-prometheus.default.svc.cluster.local:9090
    scheme: http
    env: dev

assertsClusterEnv: dev

## OPTIONAL: if not using out of the box community edition
##           and you have a trial or enterprise license
server:
  extraEnv:
    - name: ASSERTS_LICENSE_AUTH_TOKEN
      value: "<YOUR-AUTH-TOKEN>"
    - name: ASSERTS_LICENSE_PRODUCT_ID
      value: "<YOUR-PRODUCT-ID>"
    - name: ASSERTS_LICENSE_KEY
      value: "<YOUR-LICENSE-KE
```
{% endcode %}

View all install configuration options [**here**](https://github.com/asserts/helm-charts/blob/master/charts/asserts/values.yaml).

#### **3. Install Asserts Helm Chart**

This command will  instal the asserts charts with overrides and create Asserts namespace

```shell
helm upgrade --install asserts asserts/asserts \
-f values.yaml \
--namespace asserts \
--create-namespace
```

### **Enable port-forward**

```bash
kubectl port-forward -n asserts deployment/asserts-ui 8080
```

### **See the data!**

You can now view the deployed frontend by visiting the following link.

[**http://localhost:8080**](http://localhost:8080)



## Update

Now that your Asserts chart is installed, you can update with the following commands:

```bash
helm repo update
helm upgrade asserts asserts/asserts -n asserts
```



## Uninstall

To uninstall Asserts and its dependencies, run the following command:

```bash
helm uninstall asserts -n asserts
```



## [Need Help](../#before-you-begin)

