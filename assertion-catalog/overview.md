---
description: >-
  Asserts growing library of assertions for cloud services and application
  frameworks.
---

# Overview

Asserts curated a library of rules that constantly run on the incoming time-series data and surface key insights as assertions. To focus on what they imply for the system, we categorize these assertions into a SAAFE model: Saturation, Amend, Anomaly, Failure, and Error. Implementation-wise, Asserts have broadly two sets of rules that raise assertions: base rules and framework-specific rules.

Our base rules are for common request and resource metrics across platforms and frameworks. The table below summarizes all these base assertions raised by our base rules.

|          | Assertion                 | SAAFE Category |
| -------- | ------------------------- | -------------- |
| Request  | RequestRateAnomaly        | Anomaly        |
|          | LatencyAverageAnomaly     | Anomaly        |
|          | LatencyAverageBreach      | Error          |
|          | LatencyP99ErrorBuildup    | Error          |
|          | ErrorRatioAnomaly         | Anomaly        |
|          | InboundClientErrorAnomaly | Anomaly        |
|          | ErrorRatioBreach          | Error          |
|          | ErrorBuildup              | Error          |
| Logging  | LoggerRateAnomaly         | Anomaly        |
|          | ErrorLogRateBreach        | Error          |
| Resource | Saturation                | Saturation     |
|          | ResourceRateAnomaly       | Anomaly        |
|          | ResourceRateBreach        | Error          |
|          | ResourceMayExhaust        | Error          |

Our framework-specific rules raise many specific assertions, which usually belong to Failure, Amend, and Saturation categories. You can read more in [Understanding SAAFE Model](../how-asserts-works/understanding-saafe-model.md). Here is a sample set of frameworks and libraries we have built-in assertions for&#x20;

![](<../.gitbook/assets/frameworks.png>)
