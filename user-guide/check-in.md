# Check-in

#### Top Insights <a id="HowAssertsWorks(WIP)-TopInsights"></a>

Assertions enable us to quantify the overall health of the system. We represent that as top insights on the default landing page.

![](../.gitbook/assets/1562312711.png)

Here we rank services by their weighted assertion score and present a thumbnail sketch of all the assertions in the system. This helps surface the hot spot in the system so a user can jump right in.

#### SLOs <a id="HowAssertsWorks(WIP)-SLOs"></a>

For a more hands-on approach, users can define SLOs based on our normalized metrics or their own metrics. For example, an SLO specification can be defined like the following and posted to our API endpoint or through our configuration UI.

```text
apiVersion: asserts/v1
kind: SLO
name: api-server-availability
indicator:
  kind: Request
  badEventCount: asserts:error:total{job="api-server", asserts_error_type="server_errors"}
  totalEventCount: asserts:request:total{job="api-server"}
entitySearch: "show service api-server"
objectives:
  - ratio: 0.99
    name: "Weekly Availability"
    window:
      kind: Rolling
      days: 7
```

Unlike assertions, SLOs are meant for capturing high-level business objectives. The specification is designed to be declarative. Asserts takes care of how it is actually tracked and integrated into our assertion and alerting framework. The web app provides a list view of all the SLOs, along with their status, and the burndown chart of the error budget.

TODO: find a better chart that has better burn-down details

![](../.gitbook/assets/slos%20%282%29%20%282%29%20%284%29.png)

#### Incidents <a id="HowAssertsWorks(WIP)-Incidents"></a>

We assume the user wants to be notified whenever an SLO is violated, so we list each violation on the Incidents page. Assertions, on the other hand, are not necessarily incidents. A user can choose to treat certain assertions as incidents by enabling notifications on them.

These incidents are grouped by SLOs and assertions and laid out in a common time frame. This is very similar to our WorkBench to help users instantly spot time-based correlation.

###  <a id="HowAssertsWorks(WIP)-Exploringtheentitygraph"></a>

