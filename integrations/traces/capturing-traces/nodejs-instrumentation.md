# NodeJS Instrumentation

OpenTelemetry trace instrumentation for the NodeJS application can be enabled either through auto instrumentation or manual instrumentation. It is recommended to use auto-instrumentation unless you have specific reasons not to do so.

We recommend using auto instrumentation.  Please follow the steps mentioned below to enable auto instrumentation for NodeJS.

1.  Please include the following dependency in the package.json

    `"@opentelemetry/api": "^1.3.0",`

    `"@opentelemetry/auto-instrumentations-node": "^0.36.0"`
2.  while launching the app please include the --require parameter as mentioned below

    `node --require @opentelemetry/auto-instrumentations-node/register`
3.  In case you have issues or you want to include only specific instrumentation you can do this by adding the following nodejs code into a file tracing.js



    ```
    'use strict'

    const process = require('process');
    const opentelemetry = require('@opentelemetry/sdk-node');
    const { HttpInstrumentation } = require("@opentelemetry/instrumentation-http");
    const { OTLPTraceExporter } = require("@opentelemetry/exporter-trace-otlp-grpc");
    const { Resource } = require('@opentelemetry/resources');
    const { SemanticResourceAttributes } = require('@opentelemetry/semantic-conventions');
    const { ExpressInstrumentation, ExpressRequestInfo } = require('@opentelemetry/instrumentation-express');

    const traceExporter = new OTLPTraceExporter()

    const sdk = new opentelemetry.NodeSDK({
      traceExporter: traceExporter,
      instrumentations: [new HttpInstrumentation(), new ExpressInstrumentation()]
    });

    // initialize the SDK and register with the OpenTelemetry API
    // this enables the API to record telemetry
    sdk.start()
      .then(() => console.log('Tracing initialized'))
      .catch((error) => console.log('Error initializing tracing', error));

    // gracefully shut down the SDK on process exit
    process.on('SIGTERM', () => {
      sdk.shutdown()
        .then(() => console.log('Tracing terminated'))
        .catch((error) => console.log('Error terminating tracing', error))
        .finally(() => process.exit(0));
    });
    ```
4.  Please note that you may have to include the following dependency in your package.json incase you are adding the above code.

    `"@opentelemetry/auto-instrumentations-node": "^0.36.0",` \
    `"@opentelemetry/exporter-trace-otlp-grpc": "^0.36.0"`
5.  While launching the nodejs application you add --require parameter as below

    `node --require tracing.js app.js`
6.  Please set the following environment variables

    OTEL\_EXPORTER\_OTLP\_TRACES\_ENDPOINT=your-otel-collector-endpoint

    OTEL\_RESOURCE\_ATTRIBUTES="service.name=your-service-name,service.namespace=your-service-namespace"
7. In case you need further details on nodejs instrumentation please refer [here](https://opentelemetry.io/docs/instrumentation/js/)
