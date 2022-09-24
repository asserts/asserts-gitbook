---
description: Frequently Asked Questions
---

# FAQ

## General

### Does Asserts offer a free trial?&#x20;

Yes, we do. See instructions on [how to get started](<README (1).md#system-requirements>).&#x20;



### What level of support and SLA does Asserts provide?

We provide chat, email, and phone support during business hours (US Pacific time and Indian Standard Time). Asserts cloud SLA guarantees 99.5% availability.&#x20;



### Is Asserts a cloud or self-hosted solution?&#x20;

Both deployment options are available. Our docs describe the requirements for both the cloud and self-hosted options. Asserts self-hosted can run even on air-gapped environments.



### What regions, zones, and clouds is Asserts available in?&#x20;

The cloud-hosted environment runs in AWS’s us-west-2 region.&#x20;



### Is Asserts hosting a Prometheus or using my Prometheus?&#x20;

Asserts focuses on finding insights from your metrics, not simply storing them, so we do not intend to be your long-term Prometheus storage. You keep your existing Prometheus and long-term Prometheus storage solutions like Thanos, Victoria Metrics, M3DB, Grafana Mimir, and Managed Prometheus from Amazon/Google et al.  \


Asserts needs access to your Prometheus metrics. This can be set up by adding [Prometheus as a Datasource](https://docs.asserts.ai/integrations/data-source/prometheus) or [remote-write](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#remote\_write) a subset of your metrics to Asserts.



### Is Asserts creating a copy of my metrics?

No, Asserts is not copying and retaining all of your metrics. In order to generate useful insights, Asserts is running queries on a subset of your metrics to build a correlation graph and recording and alerting rules to detect anomalous signals, errors, failures, etc.; [see more](https://docs.asserts.ai/how-asserts-works/how-asserts-processes-data). \
\
Asserts is not long-term storage for all your Prometheus metrics.

