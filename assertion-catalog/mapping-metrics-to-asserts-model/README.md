# Mapping Metrics to Asserts Model

Asserts is built on Open Prometheus standards and is extensible. Metrics from any components and frameworks can be mapped to the [SAAFE](../../how-asserts-works/understanding-saafe-model.md) model with a simple `yml` following Prometheus Recording Rule spec. We have already mapped metrics from the popular frameworks and components and rolling out new ones every day.

Here we will illustrate how we have already mapped metrics from Springboot and Redis to the standard Asserts metrics to detect resource **S**aturation, **A**nomalies, and changes to the deployment, aka **A**mends, and availability/latency **E**rrors.\
\
You can follow the same methodology to map custom metrics from your services.&#x20;

* [Request, Error, and Duration (RED)](request-error-and-duration-red.md)
* [Resource Utilization and Saturation](resource-utilization-and-saturation.md)
* [Failure](failure.md)
* [Amends](amend.md)
