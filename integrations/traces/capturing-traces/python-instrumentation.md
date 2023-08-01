# Python Instrumentation

OpenTelemetry trace instrumentation for the Python application can be enabled either through auto instrumentation or manual instrumentation. It is recommended to use auto-instrumentation unless you have specific reasons not to do so.

We recommend using auto instrumentation.  Please follow the steps mentioned below to enable auto instrumentation for Python.

1.  Please include the following dependencies in the requirement.txt

    `opentelemetry-distro`\
    `opentelemetry-exporter-otlp`
2.  Run the following command to add all the required instrumentation packages automatically

    `opentelemetry-bootstrap -a install`
3.  While launching Python please add `opentelemetry-instrument` as mentioned below

    `opentelemetry-instrument python myapp.py`
4.  Please add the following environment variables

    `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT= your-otel-collector-endpoint`\
    `OTEL_RESOURCE_ATTRIBUTES: "service.name=your-service-name,service.namespace=your-service-namespace"` \
    `OTEL_METRICS_EXPORTER: "none"`
5. For further information on instrumenting the Python application please refer [here](https://opentelemetry.io/docs/instrumentation/python/)
