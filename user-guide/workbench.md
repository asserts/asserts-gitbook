# RCA with the Workbench

The traditional way of troubleshooting usually involves pulling out a few dashboards, open a metrics browser and add a few metrics, and perhaps open a few more browser windows for logs and other things. Often you realize the dashboards are out-of-date, or you don’t know which metrics to pull, or it takes you 5 minutes just trying to zoom into the time range to focus. The list of annoyance can be quite long.

Asserts has automatically surfaced all the assertions in the system and enriched them with all the context. At times, just a glance over Top Insights, or a search into the graph can reveal what’s going on. For more hands-on troubleshooting, we build a Workbench for manually analyzing assertions.

The workflow for the Workbench is similar to a pinboard. Whether a user is just glancing through top insights, checking incidents, or exploring the entities in the graph, there is always a pin button “+” or “Add” to add entities, assertions, or the result of a search expression to the Workbench. Once they are in the Workbench, they are plotted in the same time frame, so if there is any causality in between, it will pop up right in front of you.

![Correlate Assertions from connected services in Interactive Workbench](../.gitbook/assets/wb-docs.gif)

The search box also works for the Workbench to help quickly add more entities for root cause analysis. We also have logging system integration in the Workbench, so the user can quickly jump into the logs with the time range pre-populated.

The timeline view is not the only view option. A user can also choose the graph view to evaluate the impact of the current issues and spot spatial correlation on the graph. In addition, the Mindmap view provides a way to walk down the assertions by categories and assertion types instead of entities. This view helps to spot the common problem across many different entities.

### &#x20;<a href="#howassertsworks-wip-assertionmanagement" id="howassertsworks-wip-assertionmanagement"></a>
