# Understanding SAAFE Model

Existing taxonomies for time-series data ([the four golden signals](https://sre.google/sre-book/monitoring-distributed-systems/), [the RED method](https://www.weave.works/blog/the-red-method-key-metrics-for-microservices-architecture/), or [the USE method](https://www.brendangregg.com/usemethod.html), etc.) are most concerned about the nature of each type of data. Asserts's terminology is slightly different, as we aspire to help our customers to focus on what they imply, not where they come from. Upon receiving raw time-series data, Asserts platform instruments them with our domain knowledge, surfaces key insights as _assertions_, and provides a UI to facilitate the use of assertions for monitoring and troubleshooting.&#x20;

Hence, based on what they imply for the system, assertions are categorized in a SAAFE model: _Saturation_, _Amend_, _Anomaly_, _Failure,_ and _Error_. They may belong to three different severity levels: _info_, _warning_, and _critical,_ color-coded in the UI as blue, yellow, and red, respectively.

## Saturation

We monitor hardware resources like CPU, memory, and disk. We also monitor software objects like client connections that come with built-in limits. When their usage is close to their limits, a saturation assertion happens. Such assertion does not necessarily indicate an immediate problem but is a cause for concern.&#x20;

The detection mechanism of saturation resembles the following simple PromQL rule:

```
asserts:resource > asserts:resource:threshold
```

We quantify resource usage either as a percentage or an absolute value. Either way, the usage value is compared to two static thresholds. One is for warning, and the other for critical. They are not overlapping, so when the critical saturation fires, the warning assertion is suppressed. In the following example, CPU load experienced both warning and critical.

![](<.gitbook/assets/image (3).png>)

Different types of resources need different thresholds. Even for the same resource type, like CPU, its usage measured by [cAdvisor](https://github.com/google/cadvisor) or [node exporter](https://github.com/prometheus/node\_exporter) can be different, thus may need different threshold values. Asserts provides default values per resource type, and in some cases, per source/exporter. Customers can modify them. For more fine-grained control, they can also supply threshold values on the container level.

To simplify the customization, the three levels (resource type, metric source, and container) are hierarchical in this order. For example, a customer can define a global threshold of 85% for memory usage, but if reported from the Redis exporter, 80%, and if it's on a Redis instance for a particular memory-critical application, 70%.

Refer to [Assertion Management ](user-guide/assertion-management.md)on how to make these customizations.

## Amend

When changes are initiated by humans, either directly as new deployment or indirectly as auto-scaling kicks in, needless to say, they often impact the health of the system. Asserts captures these changes as amend assertions. They are usually on _info_ level, as blue marks in entity view or the Workbench. They are helpful for correlation analysis.&#x20;

Out of the box, Asserts detect the following amends

* Version updates for services, exporters, and runtimes
* Kubernetes deployment events like container image update, deployment update, config map update, secret update
* Scaling events like node count change, pod count change
* Other domain-specific change events like shard rebalancing in elastic search, config reload in Nginx, etc

![](<.gitbook/assets/Screen Shot 2021-09-21 at 5.58.51 PM.png>)

As we expand our domain coverage, we expect to include more amend assertions in the future.

## Anomaly

Anomalies are pattern changes related to traffic. Modern cloud-native applications are built for elasticity, so they often handle traffic changes gracefully. Still, they provide context for understanding system behavior, so we capture them as _anomaly_ assertions on the _warning_ level.

They usually apply to request rate, latency, and resource consumption rate. Asserts uses a statistical approach to figure out their normal ranges. These ranges are dynamic, with daily and weekly seasonalities considered.  Anomalies thus can be defined for the case the current metric is outside of the band. A sparseness check is also put in place to reduce noise on sparse requests. Note that, unlike saturation, a breach underneath the lower bound also counts as an anomaly assertion. It can be handy for detecting loss of traffic, which sometimes implies a much bigger problem.

```
(asserts:request:rate5m < asserts:request:rate5m:anomaly_lower_threshold
or
asserts:request:rate5m > asserts:request:rate5m:anomaly_upper_threshold)
unless asserts:request:erratic_and_sparse > 0
```

An example of change detected by an `amend` assertion triggering a latency spike, an `anomaly` assertion on rule-engine service

![](<.gitbook/assets/Screen Shot 2021-10-08 at 4.34.05 PM.png>)

For these anomaly assertions, customers do not need to provide much input on thresholds, but they can customize the size of the range and the sensitivity for detecting sparse requests.

## Failure

A software system has many moving parts, each of which is expected to function in a certain way. To name a few

* each service instance or each pod should be up and responding
* a primary/standby system should only have one active at any time
* each Kafka partition should have its replication count maintained to the specified setting

Sometimes things could go wrong, and as they happen, catastrophic damage may ensue. Asserts identifies these scenarios as _failure_ assertions. They are different from saturations or anomalies because they are critical ones and usually do not involve arbitrary thresholds. An example failure assertion to assert whether a Kafka cluster violation its expectation that there is one and only one active controller looks like this

```
sum by (job) (kafka_controller_kafkacontroller_activecontrollercount) != 1
```

Failure assertions are highly domain-specific, so the list of failure assertion names is long. They come directly from our understanding of all the basic building blocks of modern cloud-native applications. We've curated a rich list of failure conditions that cover Kubernetes, Flux, Elastic Search, Kafka, MySql, Postgres, Nginx,  Redis, Traefik, and many more, and we continue to expand our coverage as time goes by.\
\
Here is an example of `failures` (pod crash looping) triggered by Traffic Spike detected via request `anomaly` assertion. &#x20;

![](<.gitbook/assets/Screen Shot 2021-09-21 at 7.02.03 PM.png>)

## Error

Besides failures, there are other erroneous events in the system about how the software handles real-world traffic. 5xx HTTP codes or error logs are classic examples of such events, and we view them as _error_ assertions. They are not catastrophic, thus tolerated, and to some extent expected in a production environment. The system still functions largely okay despite them.&#x20;

Errors are usually measured not in absolute terms, but as ratios relative to successful ones. For instance, for a REST API server, we can calculate its server error ratio as 5xx requests divided by the total requests.

```
rate(http_server_requests_seconds_count{status=~"5.."}[5m])
/
rate(http_server_requests_seconds_count[5m])
```

For Prometheus rule evaluation, we determine the error ratio as

```
rate(prometheus_rule_evaluation_failures_total[5m])
/
rate(prometheus_rule_evaluations_total[5m])
```

Because errors are usually the most important clues to discover design defects or code bugs on customer's own software, Asserts tries to identify all possible situations that we deem as error assertions.&#x20;

First, we support two basic types of error assertions

* _ErrorRatioBreach_ is to compare the current error ratio against a static threshold to capture acute error conditions that demand immediate attention and thus are critical
* _ErrorBuildup_ is to capture chronic error conditions that otherwise cannot be captured by _ErrorRatioBreach_. Asserts uses a [multi-burn-rate](https://sre.google/workbook/alerting-on-slos/) approach to monitor error building up. A fast build-up is deemed critical, while a slow build-up is a warning.

We then supplement them with two warning assertions to detect pattern changes in errors

* _ErrorRatioAnomaly_ warns against elevated overall error ratio above the normal range
* _InboundClientErrorAnomaly_ warns against elevated client error ratio (4xxs,etc) above the normal range

These assertions complement each other, but they sometimes overlap.

![](<.gitbook/assets/image (4).png>)

In addition, we've also expanded the concept of errors to latency. Even though we have latency anomaly assertions already, we understand latency impacts user experience. When a user-facing request latency is big enough, we'd like to treat it as an error condition. Latency distribution is known to have long tails, so Asserts tries not to miss out by building a couple of different assertions:

* _LatencyAverageBreach_  is to capture the overall elevated latency
* _LatencyP99ErrorBuildup_ is to capture chronic deterioation of latency. Unlike the average measurement, the underlying requests for P99 latency are usually sporadic, so we use the same fast build-up approach as what is used for _ErrorBuildup_.&#x20;

![](<.gitbook/assets/image (6) (1) (1).png>)

Because these are to capture user experience impact, they are all deemed critical.&#x20;
