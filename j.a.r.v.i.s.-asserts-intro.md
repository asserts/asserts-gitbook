# Asserts Intro

## Founding Story <a id="AssertsIntro-FoundingStory"></a>

As builders and operators of cloud applications and founding engineers of industry-leading APM products at AppDynamics, we were pinched every day; why was it still so hard to troubleshoot and optimize our app with the APM tools? We had assertions for our compile time with Unit, Component, and Integration tests but couldn’t apply the same rigor to our runtime. There wasn’t a platform where we could _transcribe our system design_ embedded in golden signals and health metrics into Runtime Assertions. When we started creating Alerts to track these failures or dashboards to view them, they were disjoint, too many to manage, and too noisy to correlate, and will go out of date too soon with our continuous release.

As engineers, we wished for a solution that was adaptive and purposefully built for _distributed systems and cloud architecture_, and most importantly, allow us to manage our _Monitoring as a Code_.

**What is Asserts?**

Asserts is the next-generation insights for your distributed, multi-cloud, hybrid applications.

With Asserts, your team can say goodbye to dashboards that are disjoint and don’t keep up with rapid releases. Your expert engineers won’t be constantly interrupted to pour over esoteric squiggly metric charts and regex stacks of logs to find the needle\(in the haystack\). And to top it off, your on-call team won’t be fatigued by alerts that don’t matter.

Let’s dig deeper to find how Asserts is different.

**Discover a Living Map of App and Infra components**

Asserts taps into your telemetry data sources and automatically builds a graph of your application and infrastructure components and indexes the graph for search.

* With our search, you can find how the components fit together in real-time.
* Our cloud component catalog is constantly evolving, and you may easily plug in your very own custom components with our DSL.

![](.gitbook/assets/1068400769%20%281%29.gif)

Instrument via SAAFE Assertions to collect the signals

Asserts curates knowledge of common runtime failure patterns and potential causes, so your team doesn’t have to research and maintain these rules.

* It continuously tracks resource _**Saturation**_, _**Amends**_ \(new deployments, scale events et al.\), request & latency _**Anomalies**_, systemic _**Failures**_, and _**Errors**_ on your [_golden signals_](https://sre.google/sre-book/monitoring-distributed-systems/) and health metrics.
* The occurrences of these assertions are annotated on the \(Knowledge\) Graph so it is easy to consume at a glance

![](.gitbook/assets/screen-shot-2021-03-04-at-1.08.43-pm%20%281%29.png)

## Explore with Unified Search <a id="AssertsIntro-ExplorewithUnifiedSearch"></a>

With our unified search, you can combine components, relations, configurations, and associated assertions to express your intent in an easy natural language expression.

* _e.g._ Try search “pods crashing due to resource limits” will return you a list of instances in a certain region that has crashed due to resource saturation

![](.gitbook/assets/1103954136%20%281%29.png)

Troubleshoot in Workbench with all the Assertions

You can use the search expression in our workbench and jump right in to view all the assertions correlated across time and space, with just the right data at your fingertip.![](.gitbook/assets/workbench-map-services%20%282%29.png)

## Releasing Soon <a id="AssertsIntro-ReleasingSoon"></a>

* **Wake up when it matters**
  * The SRE book recommends [Alerting on SLOs](https://sre.google/workbook/alerting-on-slos/), and with Asserts setting up your SLOs and tracking them is a breeze. And then finding the needles is just a click away in our workbench.
* **Never miss a beat with Top Insights**
  * With our always-on SA2FE engine, you don’t have to wait for things to fall apart. Top Insights presents a stack-ranked view of Services and Nodes that need attention based on their _assertion score_.

## How to get started? <a id="AssertsIntro-Howtogetstarted?"></a>

Asserts is cloud-agnostic and has no proprietary application agents to install. It is natively built on the open [Prometheus](https://prometheus.io/) ecosystem with 100s of [exporters](https://prometheus.io/docs/instrumenting/exporters/). You can get started by remote-writing to Asserts cloud.![](.gitbook/assets/1049362470%20%281%29.png)

