# Java Instrumentation

OpenTelemetry trace instrumentation for the Java application can be enabled either through auto instrumentation or manual instrumentation. It is recommended to use auto-instrumentation unless you have specific reasons not to do so.

Please follow the steps mentioned below to enable auto instrumentation for Java.

1. Download  opentelemetry-javaagent.jar  from[ Release](https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases) and place the JAR in your preferred directory.&#x20;
2.  Add `-javaagent:path/to/opentelemetry-javaagent.jar` and other params as mentioned below to your JVM startup arguments and launch your app:



    `java -javaagent:path/to/opentelemetry-javaagent.jar` \
    `-Dotel.metrics.exporter=none -Dotel.exporter.otel.traces.endpoint=your-otel-collector-endpoint -jar myapp.jar`
3. Please add the following environment variable\
   `OTEL_RESOURCE_ATTRIBUTES="service.name=your-service-name,service.namespace=your-service-namespace"`
4.  You can find more information on OTel instrumentation [here](https://opentelemetry.io/docs/instrumentation/java/)

    \
