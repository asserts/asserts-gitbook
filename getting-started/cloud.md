---
description: Integrate Asserts cloud with your Prometheus server(s)
---

# Cloud

![ Cloud Architecture](<../../.gitbook/assets/cloud (5).svg>)

## Prometheus Integrations

In order to generate contextualized metrics and derive useful insights from your Prometheus metric data, Asserts needs access to a subset of your raw metrics. This access can be provided by choosing one of the following integration options:

### Query

\
![](../../.gitbook/assets/query.svg)



The **Query** integration enables Asserts Cloud to run on-demand queries on your raw metrics from your specified Prometheus server(s). This works by utilizing PromQL (Prometheus Query Language) to select and aggregate time series data in real time via Prometheus' [HTTP API](https://prometheus.io/docs/prometheus/latest/querying/api/).&#x20;



### Remote-Write

<img src="../../.gitbook/assets/remote-write (2).svg" alt="
" data-size="original">



The **Remote-Write** integration allows you to send select time series data from your Prometheus server(s) to Asserts Cloud. This works by utilizing Prometheus' [remote-write](https://prometheus.io/docs/operating/configuration/#remote\_write) feature to periodically forward a batch of samples from your Prometheus server(s) to Asserts Cloud.\
\
