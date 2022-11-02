# Assertion Management

## Thresholds

Asserts has curated an assertion library pertaining to our SAAFE model. Internally they are a collection of Prometheus rules with user-configurable thresholds. We divide the threshold configuration into three sections and organize each section by assertion types

* **Request** is about rate, latency, and error assertions
  * Each **Anomaly** is checked against a dynamic range that combines standard deviation and percentage deviation. Daily and weekly seasonalities are considered. A sparseness check is also put in place to reduce noise on sparse requests.
  * Each **Breach** is checked against a static threshold. **ErrorLogSpike** is also a breach assertion
  * Client **Errors** are treated as anomalies so follow the anomaly algorithms mentioned above
  * Server **Errors** are tracked with an error budget approach, so are controlled by fast burn or slow burn factors
* **Resource** section is about CPU, Memory, Disk, Network, etc
  * Each **Saturation** works with two static thresholds, one for warning and one for critical
  * Some resources like disk have rate metrics (bytes read/write), so we have **ResourceRateAnomaly** and **ResourceRateBreach** assertions. They follow the same approach as their request counterparts
* **Health** section is mostly about all the failure assertions in various domains. We organize the domain rules by groups. Within each group, the user can edit each individual rule.

Besides the configuration for all the default thresholds, the user can also configure individual thresholds on fine-grained levels. These levels are hierarchical. For requests, if you specify a threshold on `job` level, it applies to all the request types for that job. Similarly, if you specify a threshold on `request_type` level, it applies to all the request contexts for that request type and job.

![](<../.gitbook/assets/1573322759 (1) (1) (2) (3) (1).png>)

For resources, the hierarchy starts with `source`, i.e., the exporter, then `resource_type,` and then `container`. An additional dimension is `severity`, which is independent of the granularity levels.

#### Notifications <a href="#howassertsworks-wip-notifications" id="howassertsworks-wip-notifications"></a>

As we mentioned earlier, assertions are not alerts. If you want to get notified, you can add notification rules. They are also organized into the same three sections like the thresholds, and the same hierarchical granularity levels apply. You can set notifications for an assertion whenever it fires, or when it fires on a certain job, or a certain job and request type. Free-form label conditions are also supported here.

![](<../.gitbook/assets/Screen Shot 2021-09-22 at 10.53.12 AM.png>)

Asserts internally use Prometheus Alert Manager for notification, so we support all the receivers listed on its official page (Email, Pagerduty, Slack, etc.)

#### Suppressions <a href="#howassertsworks-wip-suppressions" id="howassertsworks-wip-suppressions"></a>

Similar to notifications, you can also choose a subset of assertions to suppress, so they don’t even fire. Suppressed assertions are ignored in Asserts processing. They will not be available in Top Insights, Graph, or the Workbench. Note this is very different from an assertion that fires but does not notify.

![](<../.gitbook/assets/Screen Shot 2021-09-22 at 10.48.12 AM.png>)

#### Additions <a href="#howassertsworks-wip-additions" id="howassertsworks-wip-additions"></a>

If you have your own set of rules, you can also bring them into the Asserts system by defining custom assertion rules. You can either add them one by one on the UI or upload a rule file.

![](<../.gitbook/assets/Screen Shot 2021-09-22 at 12.04.38 PM.png>)
