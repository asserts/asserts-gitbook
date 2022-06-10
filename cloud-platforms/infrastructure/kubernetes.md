# Kubernetes

### Setup

Please refer to the [kube prometheus stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) and it’s [helm chart](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) on how to install prometheus in your k8s environment.

### Metrics and KPIs

| **Metric**                                                                                                                               | **KPI**                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ---------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>Memory</p><p>container_memory_working_set_bytes</p>                                                                                   | <p>Memory Utilization</p><p>when limit is set</p><p>container_memory_working_set_bytes / kube_pod_container_resource_limits{resource="memory"}</p><p>when limit is not set</p><p>container_memory_working_set_bytes / kube_node_status_allocatable{resource="memory"}</p>                                                                                                                                               |
| <p>CPU</p><p>container_cpu_usage_seconds_total</p><p>container_cpu_cfs_throttled_periods_total</p><p>container_cpu_cfs_periods_total</p> | <p>CPU Utilization</p><p>when limit is set</p><p>rate(container_cpu_usage_seconds_total[5m]) /</p><p>kube_pod_container_resource_limits{resource="cpu"}</p><p>When limit is not set</p><p>rate(container_cpu_usage_seconds_total[5m]) /</p><p>kube_node_status_allocatable{resource="cpu"}</p><p>CPU Throttle</p><p>rate(container_cpu_cfs_throttled_periods_total[5m]) / rate(container_cpu_cfs_periods_total[5m])</p> |
| <p>Network Bytes</p><p>container_network_transmit_bytes_total</p><p>container_network_receive_bytes_total</p>                            | <p>Data transfer rate</p><p>rate(container_network_transmit_bytes_total[5m])</p><p>rate(container_network_receive_bytes_total[5m])</p>                                                                                                                                                                                                                                                                                  |
| <p>Volume</p><p>kubelet_volume_stats_available_bytes</p><p>kubelet_volume_stats_capacity_bytes</p>                                       | <p>Volume Available</p><p>kubelet_volume_stats_available_bytes / kubelet_volume_stats_capacity_bytes</p>                                                                                                                                                                                                                                                                                                                |
| <p>PodVolumeClaim (PVC)</p><p>kube_pod_spec_volumes_persistentvolumeclaims_info</p>                                                      | <p>PVC Usage</p><p>kube_pod_spec_volumes_persistentvolumeclaims_info</p>                                                                                                                                                                                                                                                                                                                                                |

### Alerts

| **KPI**            | **Alert**                                                                        |
| ------------------ | -------------------------------------------------------------------------------- |
| Memory Utilization | **Saturation**                                                                   |
| CPU                | <p><strong>Saturation</strong></p><p><strong>CPUThrottlingSustained</strong></p> |
| Network Bytes      | **ResourceRateAnomaly**                                                          |
| Volume             | **KubeVolumeLow**                                                                |

#### Failure Alerts

* KubePodCrashLooping
* KubePodNotReady
* KubeDeploymentGenerationMismatch
* KubeDeploymentReplicasMismatch
* KubeStatefulSetReplicasMismatch
* KubeStatefulSetGenerationMismatch
* KubeStatefulSetUpdateNotRolledOut
* KubeDaemonSetRolloutStuck
* KubeDaemonSetNotScheduled
* KubeDaemonSetMisScheduled
* KubeJobFailed
* KubeHpaReplicasMismatch
* KubeHpaMaxedOut
* KubeContainerWaiting
* KubeCronJobRunning
* KubeJobCompletion
* KubeCPUOvercommit
* KubeMemoryOvercommit
* KubeCPUQuotaOvercommit
* KubeMemoryQuotaOvercommit
* KubeQuotaExceeded
* KubeAPILatencyHigh
* KubeAPIErrorsHigh
* KubeClientCertificateExpiration
* KubeClientCertificateExpiration
* KubeAPIDown
* KubeAPIGone
* KubeNodeNotReady
* KubeNodeUnreachable
* KubeletTooManyPods
* KubeNodeReadinessFlapping
* KubeletPlegDurationHigh
* KubeletPodStartUpLatencyHigh
* KubeletDown
* KubeletGone

### Dashboard

All workloads running on k8s are discovered as a Service in Asserts and have a Service KPI Dashboard. For each instance in the Service there is also a ServiceInstance dashboard. These dashboards show the key resource KPIs for memory, cpu, disk and network bytes transmitted. In addition to the Service KPI dashboard, the following additional dashboards are also available

### Cluster

![](../../.gitbook/assets/Kubernetes\_Cluster.png)

### Namespace

![](../../.gitbook/assets/Kubernetes\_Namespace.png)

### Pod

![](../../.gitbook/assets/Kubernetes\_Pod.png)

### Node

![](../../.gitbook/assets/Kubernetes\_Node.png)

### Proxy

![](../../.gitbook/assets/Kubernetes\_Proxy.png)

### API Server

![](../../.gitbook/assets/Kubernetes\_ApiServer.png)
