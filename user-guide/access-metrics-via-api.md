---
description: How to access your metric data in Asserts via the Grafana Prometheus api
---

# Access Metrics via API

Asserts ships with an embedded Grafana instance which allows you to explore your Asserts metrics as well as the metrics you expose to Asserts as described [here](metrics.md).&#x20;

In addition to exploring via the Grafana UI, you can also access them via api.

To do so you will need to create api credentials. You can either do this for your specific user account or create global credentials associated with your instance.

## Creating Credentials

### User Credentials

You can create API credentials for yourself as well as retrieve the instant and range query endpoints on the API Credentials page in the Your Settings section of the settings menu. Note that the urls are only visible to users with the proper permission.

<figure><img src="../.gitbook/assets/API_Credentials___Asserts.png" alt=""><figcaption></figcaption></figure>

### Global Credentials

You can create global API credentials as well as retrieve the instant and range query endpoints on the API Credentials page in the Your Settings section of the settings menu.

<figure><img src="../.gitbook/assets/Global_API_Credentials___Asserts.png" alt=""><figcaption></figcaption></figure>

## Accessing Prometheus through Grafana

All calls to the embedded Grafana instance must be proxied through the Asserts api-server. This will give you the ability to set role level access per user within Asserts

Once you have your credentials, send your token & key as basic auth credentials to the Asserts api-server. See the [Prometheus API documentation](https://prometheus.io/docs/prometheus/latest/querying/api/) for details on calling their apis.

### Sample Instant Query

```
curl "https://key:token@you.app.asserts.ai/api-server/v1/grafana/api/datasources/proxy/<your_datasource_id>/api/v1/query?query=up"
```

### Sample Instant Response

```
{
  "status": "success",
  "data": {
    "resultType": "vector",
    "result": [
      {
        "metric": {
          "__name__": "up",
          "asserts_env": "dev",
          "asserts_site": "dev",
          "cluster": "asserts-dev",
          "container": "api-server",
          "endpoint": "http",
          "instance": "10.8.80.172:8030",
          "job": "api-server",
          "namespace": "asserts",
          "pod": "api-server-5544b88d46-hbkvk",
          "service": "api-server"
        },
        "value": [
          1681837357,
          "1"
        ]
      },
      {
        "metric": {
          "__name__": "up",
          "asserts_env": "dev",
          "asserts_site": "dev",
          "cluster": "asserts-dev",
          "container": "assertion-detector",
          "endpoint": "http",
          "instance": "10.8.80.55:8040",
          "job": "assertion-detector",
          "namespace": "asserts",
          "pod": "assertion-detector-567788c969-6zwfv",
          "service": "assertion-detector"
        },
        "value": [
          1681837357,
          "1"
        ]
      }
    ]
  }
}

```

## Formatting Metric Output

Formatting metric data into something like a CSV file will make it much more sharable if you're not consuming the metrics into code.&#x20;

### Instant Metrics to CSV Sample

Here is a small sample Python script to run a PromQL query and output the metrics, labels & values as CSV

```python
import csv
import requests
import sys

"""
A simple program to print the result of a Prometheus query as CSV.
"""

if len(sys.argv) != 2:
    print('Usage: {0} promQL'.format(sys.argv[0]))
    sys.exit(1)

response = requests.get('https://<token>:<secret>@<your tenant>.app.dev.asserts.ai/api-server/v1/grafana/api/datasources/proxy/<your DS id>/api/v1/query'.format(sys.argv[1]),
        params={'query': sys.argv[1]})
results = response.json()['data']['result']

# Create list of all label names
labelnames = set()
for result in results:
      labelnames.update(result['metric'].keys())

# remove redundant __name__ label
labelnames.discard('__name__')
labelnames = sorted(labelnames)

writer = csv.writer(sys.stdout)
# Write the column headers
writer.writerow(['name', 'timestamp', 'value'] + labelnames)

# Write the results
for result in results:
    l = [result['metric'].get('__name__', '')] + result['value']
    for label in labelnames:
        l.append(result['metric'].get(label, ''))
    writer.writerow(l)


```
