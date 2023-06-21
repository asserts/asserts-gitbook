# eBPF Probe

Asserts eBPF Probe provides a Prometheus metrics endpoint for service to service network connections. Asserts uses this metric data as a feed to build the Entity Graph.&#x20;

Asserts eBPF Probe may optionally be deployed to Kubernetes clusters where Prometheus metrics are being collected. It may be optionally included when installing Asserts onto a cluster, see [Helm Chart](self-hosted/helm-chart.md).

On Kubernetes clusters where Prometheus metrics are being collected and Asserts is not installed, the eBPF Probe may be installed on its own.

### Installing the Chart

There is a separate chart for Asserts eBPF Probe.

```
helm repo add asserts https://asserts.github.io/helm-charts
helm repo update asserts
helm upgrade ebpf-probe asserts/ebpf-probe \
    --install \
    --namespace asserts \
    --create-namespace
```

The chart creates a daemon set and a Pod Monitor for the Prometheus Operator. If the Pod Monitor requires additional labels to be picked up by the local Prometheus Operator configuration it can be set with:

```
--set 'podMonitor.extraLabels.release=prometheus'
```
