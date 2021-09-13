# Understanding SAAFE Model

Existing taxonomy for time-series data \([the four golden signals](https://sre.google/sre-book/monitoring-distributed-systems/), [the RED method](https://www.weave.works/blog/the-red-method-key-metrics-for-microservices-architecture/), or [the USE method](https://www.brendangregg.com/usemethod.html), etc\) are mostly concerned about the nature of each piece of data. We at Asserts, however, aspire to help our customers to focus on what they imply, not where they come from. We build Asserts platform to leverage our domain knowledge for instrumentation, and surface key insights as _assertions_ out of the raw metrics. We categorize assertions exactly on what they imply for the system, and build a UI to facilitate the workflow to use them for monitoring and troubleshooting. 

Based on the implication for the system, our assertion model can be expressed as the SAAFE model: _Saturation_, _Amend_, _Anomaly_, _Failure_ and _Error_. 





