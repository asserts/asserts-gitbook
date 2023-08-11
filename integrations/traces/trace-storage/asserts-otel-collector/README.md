# Asserts OTel Collector

<figure><img src="../../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption><p>Asserts OTel Collector</p></figcaption></figure>

The Asserts OTel Collector helps reduce trace volumes significantly by retaining only slow and error traces. It also generates span metrics which are used by Asserts to discover the services and observe RED metrics.&#x20;

The Asserts OTel Collector needs to be installed in your infrastructure and all the OTel agents would need to be configured to send their traces to the Asserts collector. The Asserts OTel collector can be installed either as an [ECS Service](aws-ecs.md) or run as a [docker container](docker.md).

