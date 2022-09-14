---
description: Frequently Asked Questions
---

# FAQ

## General

### Does Asserts offer a free trial?&#x20;

Yes, we do. Schedule a call with us to get started.&#x20;



### What level of support and SLA does Asserts provide?

We provide chat and email support during business hours (US Pacific time), and our SLA guarantees 99.5% availability.&#x20;



### Is Asserts a cloud or self-hosted solution?&#x20;

Both deployment options are available. Our docs describe the requirements for both the cloud and self-hosted options.&#x20;



### What regions, zones, and clouds is Asserts available in?&#x20;

The cloud-hosted environment runs in AWS’s us-west-2 region.&#x20;



### Is Asserts hosting a Prometheus or using my Prometheus?&#x20;

Asserts focuses on finding insights from your metrics, not simply storing them, so we do not provide a hosted Prometheus instance. You keep your existing Prometheus and configure it to send metrics to Asserts via remote write.&#x20;



### What if I want to host my own time series database?&#x20;

Asserts doesn’t replace your time series database, so you are free to store your metrics however you like. The only requirement is that you send the metrics you’d like us to analyze in the OpenMetrics format. If you are running Prometheus already, the remote write feature is an easy way to do that.&#x20;



### Is Asserts creating a copy of my metrics?

No, Asserts is not copying and retaining all of your metrics. In order to generate useful insights, Asserts does need a subset of your metric data but we don’t provide long term storage for raw metric data.

