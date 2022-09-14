---
description: Integrate Asserts cloud with your Prometheus server(s)
---

# Cloud

![ Cloud Architecture](<../../.gitbook/assets/cloud (5).svg>)

## Prometheus Integrations

In order to generate <mark style="color:blue;">**Contextualized Metrics**</mark> and derive useful insights from your Prometheus metric data, Asserts needs access to a subset of your <mark style="color:yellow;">**Raw Metrics.**</mark> This access can be provided by choosing one of the following integration options:

*   [Remote-Write](cloud.md#remote-write)

    <img src="../../.gitbook/assets/remote-write (2).svg" alt="
    " data-size="original">

    \

* [Federation](cloud.md#federation)\
  ![](../../.gitbook/assets/federation.svg)
* [Query](cloud.md#query)\
  ![](../../.gitbook/assets/query.svg)



### Remote-Write

The **Remote-Write** integration allows you to send select time series data from your Prometheus server(s) to Asserts Cloud. This works by utilizing Prometheus' [remote-write](https://prometheus.io/docs/operating/configuration/#remote\_write) feature to periodically forward a batch of samples from your Prometheus server(s) to Asserts Cloud.\
\
Of the 3 methods, this is typically the easiest to setup and the most popular way to send Prometheus data into 3rd party system. Though long term storage for raw metric data.



### Federation

The **Federation** integration enables Asserts Cloud to scrape selected time series data from your specified Prometheus server(s). This works by utilizing Prometheus' [federation](https://prometheus.io/docs/prometheus/latest/federation/#federation) feature to periodically fetch a batch of samples from your Prometheus server(s) and copy them into Asserts Cloud.

For Asserts Cloud to be able to scrape your specified Prometheus server(s), you have to open up firewall ports and secure multiple servers.

Good Fit:\
Single Prometheus Server for Cloud to scrape\
Ok with sending a copy of data&#x20;

\
Bad Fit:\
Multiple Prom Servers\
Prometheus server(s) have IP addresses that are constantly changing, or on unstable networks



### Query

The **Query** integration enables Asserts Cloud to run on-demand queries on your <mark style="color:yellow;">**Raw Metrics**</mark> from your specified Prometheus server(s). This works by utilizing PromQL (Prometheus Query Language) to select and aggregate time series data in real time via Prometheus' [HTTP API](https://prometheus.io/docs/prometheus/latest/querying/api/).&#x20;

Good Fit:\
No need to manage a \
Not Ok with sending a copy of data\
Have Managed LTS service for Prom data\
