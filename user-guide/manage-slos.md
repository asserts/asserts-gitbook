# Manage SLOs

#### SLOs <a href="#howassertsworks-wip-slos" id="howassertsworks-wip-slos"></a>

Unlike assertions, SLOs are meant for capturing high-level business objectives and also help reduce alert fatigue. Asserts makes it extremely easy to define SLOs for your services. Asserts automatically tracks the SLO, reports violations as Incidents that are integrated into the assertion and alerting framework.  You can define SLOs through the UI or through the API

**Availability SLO using Simple UI**

Defining an SLO through the UI is very simple. Navigate to the `Manage SLOs` page and click the add new SLO, select your service, and optionally one or more specific APIs. You can also associate the SLO definition with a search expression or a saved search. This enables quick navigation from SLO UI to [Top Insights](https://docs.asserts.ai/user-guide/check-in#howassertsworks-wip-topinsights) or the [WorkBench](https://docs.asserts.ai/user-guide/rca-with-the-workbench) to do an RCA in response to an incident.&#x20;

![Define an availability SLO using the simplified UI](<../.gitbook/assets/Screen Shot 2022-01-05 at 7.32.03 PM.png>)

#### Availability SLO using Advanced UI

There is also an advanced UI that allows you to use your own custom prometheus queries to define the SLO.

![Define an availability SLO using the advanced UI](<../.gitbook/assets/Screen Shot 2022-01-05 at 7.35.17 PM.png>)

#### Latency SLO using Simple UI

![Define a latency SLO using the Simple UI](<../.gitbook/assets/Screen Shot 2022-01-05 at 7.47.34 PM.png>)

![Define a latency SLO using Advanced UI](<../.gitbook/assets/Screen Shot 2022-01-05 at 7.55.26 PM (1).png>)

**SLO Definition API**

SLOs can also be defined through an API. Here is an example of a SLO API request to define an SLO.&#x20;

```
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

**SLO List Page**

The SLO list page lists all the SLOs along with their status and a summary of budget utilization and incidents.

![List of SLOs with their budget utilization, incident status and summary](<../.gitbook/assets/Screenshot 2021-10-26 at 3.30.33 PM.png>)

**SLO Burndown and Incident Timeline**

Clicking an SLO Objective navigates to a view that shows the error budget burndown and the underlying metric. Occurrences of incidents are highlighted on the timeline. Clicking a highlighted incident will show the incident duration and budget burndown. The tooltip has a navigation to go to the **TopInsights** view using the search expression provided in the SLO definition and the time window of the incident

![](<../.gitbook/assets/Screen Shot 2021-09-30 at 4.37.17 PM.png>)

#### Incidents <a href="#howassertsworks-wip-incidents" id="howassertsworks-wip-incidents"></a>

We assume the user wants to be notified whenever an SLO is violated, so we list each violation on the Incidents page. Assertions, on the other hand, are not necessarily incidents. A user can choose to treat certain assertions as incidents by enabling notifications on them.

These incidents are grouped by SLOs and assertions and laid out in a common time frame. This is very similar to our WorkBench to help users instantly spot time-based correlation.

![](../.gitbook/assets/Incident.jpg)
