# Logs

When doing RCA in the workbench, one can pick a specific time window when a problem occurred and directly jump to see the logs for that component in that time window. &#x20;

Currently, log integration is supported for **Kibana**, **Graylog,** and **Loki**

![View logs for the exact problem window](<../.gitbook/assets/Screenshot 2021-10-26 at 2.08.19 PM.png>)

## Log Configuration

**Environment** The environment for which the configuration is applicable

**URL** The base URL of the log analytics UI

**Date Format** The format for start and end time inputs in the search request. This is specified using [Java Date Format](https://docs.oracle.com/javase/7/docs/api/java/text/SimpleDateFormat.html)

**Default Search Text** If there is a default search text for this environment. For e.g. _cluster:"asserts-dev"_

**Error Filter** Search text to identify error messages. For e.g. _log\_level: ERROR._ This will be automatically added when viewing logs for **errors**

**Correlation labels** The set of labels whose values are available as fields in the log message.

**Label to Log Field Mapping (optional)** Optionally, the correlation labels can be mapped to the log fields to enable a precise search of logs related to the component, problem and time-window.&#x20;

**Index** The index id. This is applicable only for **Kibana**

## Kibana

![Kibana Configuration to a custom Kibana Service](<../.gitbook/assets/Screenshot 2021-10-21 at 11.42.27 AM.png>)

## Graylog

![Graylog Configruation to a custom Graylog Service](<../.gitbook/assets/Screenshot 2021-10-21 at 11.44.50 AM.png>)

## Loki

<figure><img src="../.gitbook/assets/Screenshot 2022-11-07 at 12.24.46 PM.png" alt=""><figcaption><p>Loki Log Configuration to a custom Loki Service</p></figcaption></figure>

#### Loki Log-Specific Configuration

**Org Id**: Loki Org Id in Grafana

**Data Source**: Loki DataSource name in Grafana

&#x20;
