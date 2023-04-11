---
description: Using Prometheus libraries or exporters generates the following metric counts
---

# Typical Metric Counts

| Technology                   | Metric Count |
| ---------------------------- | ------------ |
| NodeJS Express               | 317          |
| Go                           | 62           |
| MySQL                        | 4162         |
| Python Flask                 | 75           |
| RabbitMQ                     | 1081         |
| Redis                        | 234          |
| Java Springboot Micrometer   | 572          |
| MongoDB                      | 2373         |
| Prometheus Node Exporter (1) | 1328         |
| Kube State Metrics           | 4894         |
| Asserts eBPF Probe (1)       | 145          |
| Loki                         | 2422         |
| Jaeger All In One            | 945          |
| Docker                       | 455          |
| Nomad                        | 139          |
| Consul                       | 130          |
| cAdvisor                     | 2229         |

(1) - Per node in the cluster

These figures are a base count and the number of metrics will increase with more endpoints in a service, more queues, more databases, more containers, etc.
