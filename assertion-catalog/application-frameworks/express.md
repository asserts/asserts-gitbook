# Express

### Setup

We recommend the [Express Middleware exporter](express.md). To set up this exporter, just add it s a dependency in your NodeJS package.json

```
{
  ...
  "dependencies": {
      ...
      "express-prometheus-middleware": "1.2.0"     
  }
}
```

Import the package and add it to the Express app.

```
...
const promMid = require('express-prometheus-middleware');
...

app.use(promMid({
    metricsPath: '/metrics',
    collectDefaultMetrics: true,
    requestDurationBuckets: [0.1, 0.5, 1, 3, 5, 10],
    /**
     * Uncomenting the `authenticate` callback will make the `metricsPath` route
     * require authentication. This authentication callback can make a simple
     * basic auth test, or even query a remote server to validate access.
     * To access /metrics you could do:
     * curl -X GET user:password@localhost:9091/metrics
     */
    // authenticate: req => req.headers.authorization === 'Basic dXNlcjpwYXNzd29yZA==',
    /**
     * Uncommenting the `extraMasks` config will use the list of regexes to
     * reformat URL path names and replace the values found with a placeholder value
    */
    // extraMasks: [/..:..:..:..:..:../],
    /**
     * The prefix option will cause all metrics to have the given prefix.
     * E.g.: `app_prefix_http_requests_total`
     */
    // prefix: 'app_prefix_',
    /**
     * Can add custom labels with customLabels and transformLabels options
     */
    // customLabels: ['contentType'],
    // transformLabels(labels, req) {
    //   // eslint-disable-next-line no-param-reassign
    //   labels.contentType = req.headers['content-type'];
    // },
  }));
```

If the request URLs include ids. for e.g. **GET /order/1234567**, the URLs need to be captured as **/order/{id}** to avoid metric explosion. In this example, the regexp **\[0-9]+** which captures all numbers **1234567** can be specified in the **extraMasks** list of regular expressions.&#x20;

```
extraMasks: ['[0-9]+']
```

You can verify the presence of the following metrics

* http\_requests\_total
* http\_request\_duration\_seconds\_count
* http\_request\_duration\_seconds\_sum
* http\_request\_duration\_seconds\_bucket

### Metrics

<table data-header-hidden><thead><tr><th></th><th width="400"></th></tr></thead><tbody><tr><td><strong>Metric</strong></td><td><strong>KPI</strong></td></tr><tr><td><p><strong>Requests</strong></p><p>http_requests_total</p></td><td><p>Request Rate</p><p>rate(http_requests_total[5m])</p></td></tr><tr><td><p><strong>Errors</strong></p><p>http_requests_total</p></td><td><p>Error Ratio (server errors)</p><p>rate(http_requests_total{status=~"5.."}[5m])/ rate(http_requests_total[5m])</p><p>Error Ratio (client errors)</p><p>rate(http_requests_total{statusCode=~"4.."}[5m])/ rate(http_requests_total[5m])</p></td></tr><tr><td><p><strong>Latency</strong></p><p>http_request_duration_seconds</p></td><td><p>Latency Average</p><p>rate(http_request_duration_seconds_sum[5m])/ rate(http_request_duration_seconds_count[5m])</p><p>Latency P99</p><p>histogram_quantile (<br>0.99,<br>sum(rate(http_request_duration_seconds_bucket[1m]) > 0)<br>by (le)<br>)</p></td></tr></tbody></table>

### Alerts

| **KPI**                                  | **Alerts**                                                                                                                              |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Request Rate                             | **RequestRateAnomaly**                                                                                                                  |
| Error Rate                               | <p><strong>ErrorRatioBreach</strong></p><p><strong>ErrorBuildup</strong> based on a 99.9 SLO</p>                                        |
| <p>Latency Average</p><p>Latency P99</p> | <p><strong>LatencyAverageBreach</strong></p><p><strong>LatencyAverageAnomaly</strong></p><p><strong>LatencyP99ErrorBuildup</strong></p> |

### Dashboards

#### Service KPI Dashboard

This dashboard has the following KPIs from resources and requests:

* Request Rate
* Latency Average
* Latency P99
* Error Rate
* CPU %
* CPU Cores Used
* CPU Throttle
* Memory %
* Memory Bytes
* Disk Usage
* Network Usage

![](<../../.gitbook/assets/image (18).png>)
