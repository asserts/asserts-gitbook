# A New Age For Time-Series Data

In the land of observability, time-series data, aka metrics, have played a central role. Traditional monitoring products capitalize on their collection and analysis but keep their format as a proprietary implementation detail. Then the need for interoperability comes along. In the cloud-native world, open-source approaches like [Prometheus](https://prometheus.io/) have gained wide adoption. [Off-the-shelf metric exporters](https://prometheus.io/docs/instrumenting/exporters/) flourished. Developers build their software with metric-publishing a built-in. This changed the ecosystem. How to collect data is much less the focus than how to consume them.

Combing through the ocean of metrics can be a daunting task. PromQL helps, but it requires its user to have deep domain knowledge of the time series data itself. Dashboards help too, but they need considerable human effort to keep up with constant-changing software systems rolled out from a CI/CD pipeline.

Asserts positions itself as a new way to look at time-series data, to automatically bridge the gap between machine-generated data and human understanding of the system. Our key observation is that these metrics have embedded sufficient contextual information within themselves. All we need is to tap into a system’s metrics stream, and we can discover all the underlying infrastructure and application entities like Nodes, Pods, Services, with which we can better utilize the time-series data

* we can automatically group metrics together in curated dashboards
* we have the right context to assert what’s going on for certain performance problems
* we can leverage their relationships to further assert the correlation/causality between things
* we can propagate and rank these assertions and surface the most important insights

