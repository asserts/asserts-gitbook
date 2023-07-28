# Docker

Alternatively, you can deploy the docker container for the Asserts Collector `asserts/otel-collector:latest-release-all-exporters` and configure it to as per your requirements. The following are the environment variables that can be used to configure the collector

<mark style="color:orange;">**TRACE\_STORE**</mark>&#x20;

The trace store backend. Allowed values are&#x20;

* **AWS-XRAY**  Specify this value for AWS X-Ray
* **GOOGLE-CLOUDTRACE** Specify this value for Google CloudTrace.&#x20;
  * **GCP\_PROJECT\_NAME** The GCP Project name
  * _**Note on Authentication:**_ Please note that Google credentials need to be passed as a json file. The path of this json can be configured through an environment variable. See [Google documentation](https://cloud.google.com/docs/authentication/application-default-credentials#GAC) for more details. You will have to set this environment variable and mount the credentials file while launching the container
* **OTLP-HTTP** Specify this value for any OTLP compatible backend with traces sent over HTTP
* **OTLP** Specify this value for any OTLP compatible backend with traces sent over gRPC. You can choose this value when sending traces to [Grafana Tempo](../../trace-store-integration/grafana-tempo.md).

<mark style="color:orange;">**ASSERTS\_TENANT**</mark>

Set this to the Asserts tenant name. In self-hosted mode, set this to `bootstrap`

<mark style="color:orange;">**ASSERTS\_ENV**</mark>

Asserts has first class support for segregating all data by environments. Set this value to the environment in which the collector is being deployed.

<mark style="color:orange;">**ASSERTS\_SITE**</mark>

In production environments, applications can be deployed in multiple regions. The collector should be collocated with the source of the telemetry. The region can be specified through this environment variable.

<mark style="color:orange;">**ASSERTS\_SERVER\_API\_ENDPOINT**</mark>

The Asserts collector connects to the Asserts server to retrieve configuration information.

<mark style="color:orange;">**ASSERTS\_SERVER\_USERNAME**</mark>

<mark style="color:orange;">**ASSERTS\_SERVER\_PASSWORD**</mark>

<mark style="color:orange;">**LOG\_LEVEL**</mark>

