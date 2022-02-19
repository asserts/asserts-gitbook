---
description: Introduction
---

# Asserts

## **Founding Story**

As builders and operators of cloud applications and founding engineers of industry-leading APM products, we were pinched every day; why was it still so hard to troubleshoot and optimize our app with the modern APM tools? We had [assertions](https://en.wikipedia.org/wiki/Assertion\_\(software\_development\)) for our compile time with Unit, Component, and Integration tests but couldn’t apply the same rigor to our runtime. There wasn’t a platform to _transcribe our system design_ embedded in the [four golden signals](https://sre.google/sre-book/monitoring-distributed-systems/#xref\_monitoring\_golden-signals) and health metrics into Runtime Assertions. When we started creating Alerts to track these failures and dashboards to view the metrics, they were disjoint, too many to manage, too noisy to correlate, and will go out of date too soon with our continuous release.

As engineers, we wished for an adaptive and purposefully built solution for _distributed systems and cloud architecture_, and most importantly, that allowed us to manage our _Monitoring as a Code_.

**What is Asserts?**

Asserts is the next-generation insights for your distributed, multi-cloud applications.

With Asserts, your team can say goodbye to disjointed dashboards that don’t keep up with rapid releases. Your expert engineers won’t be constantly interrupted to pore over esoteric metric charts and sift through an ocean of disjointed logs hoping to stumble upon the needle in the haystack. And to top it off, your on-call team won’t be fatigued by alerts that don’t matter.

Let’s dig deeper to find how Asserts is different.

## **Features**

### **Discover a Living Map of App and Infra components**

Asserts taps into your telemetry data sources (app metrics), automatically builds a graph of your application and infrastructure components, and indexes the graph for search.

* With our search, you can find how the components fit together in real-time and view KPIs in the built-in Grafana dashboard.

![](<.gitbook/assets/Screen Shot 2021-09-17 at 12.30.30 AM.png>)

Our [cloud component catalog](cloud-platforms.md) is constantly evolving.

### Instrument **via SAAFE Assertions to collect the symptoms and causes**

Asserts curates knowledge of common runtime failure patterns and potential causes, so your team doesn’t have to research and maintain these rules.

It continuously tracks resource _**Saturation**_**s**, _**Amends**_ (new deployments, scale events et al.), request & latency _**Anomalies**_, systemic _**Failures**_, and _**Errors**_ on your [_golden signals_](https://sre.google/sre-book/monitoring-distributed-systems/#xref\_monitoring\_golden-signals) and health metrics.

The occurrences of these assertions are annotated on the (Knowledge) Graph, so it is easy to consume at a glance.&#x20;

![](.gitbook/assets/1.png)

### **Explore with Unified Search**

With our unified search, you can combine components, relations, configurations, and associated assertions to express your intent in an easy natural language expression.

_e.g.,_ Search “Pods `crashing` on Nodes with high `cpu:load`”&#x20;

![](<.gitbook/assets/Screen Shot 2021-09-16 at 11.34.26 PM.png>)

### **Wake up when it matters**

The SRE book recommends [Alerting on Service Level Objectives (SLO)](https://sre.google/workbook/alerting-on-slos/), to track`"what's broken"`and with Asserts setting up your SLOs and tracking your error budget is a breeze. And then finding "why it's broken" is just a click away in our Assertion workbench.

![](<.gitbook/assets/Screen Shot 2021-09-30 at 9.19.41 AM.png>)

### **Spot issues quickly with Top Insights**

With our always-on Assertions, you don’t have to wait for SLOs to breach and Alerts to fire. Top Insights presents a stack-ranked view of Services / Nodes that need attention based on their `assertion score`. \
And then `Open in Workbench` to find the root cause.

![](<.gitbook/assets/Screen Shot 2021-09-17 at 12.32.50 AM.png>)

### **Troubleshoot in Workbench with all the Assertions**

In our assertion workbench, dig in to view all the possible causes correlated across time and space, with just the right metrics and logs at your fingertip.

_e.g.,_ an amend (new deployment) on `api-server`triggered a spike in error rate on an endpoint `/slo/incidents`. Jump to `Dashboard` or `View Logs` to see contextual logs in your existing log store, like Kibana, Graylog, et al.

![](<.gitbook/assets/Screen Shot 2021-09-17 at 12.38.03 AM.png>)

### ****
