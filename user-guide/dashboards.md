# Dashboards

The Dashboards tab opens the dashboard management UI from the embedded Grafana. Asserts have created a library of dashboards for all the entity types like Services, Pods, Nodes, etc. We have also curated a selection of community dashboards for various frameworks like JVM, Go, Kafka, etc. Each entity type can link to multiple dashboards from above. When the user opens the KPI tab in Entity Graph or Workbench, Asserts displays these dashboards with pre-populated variables.

<figure><img src="../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

We don't expect users to modify these dashboards as we ship them as Asserts artifacts. However, users can always import their own dashboards and link them to specified entity types for customization.

To limit the scope of the linking, one can optionally supply a PromQL query so only entities with matching labels in the query result will be linked.

<figure><img src="../.gitbook/assets/screencast 2023-03-17 16-26-56.gif" alt=""><figcaption></figcaption></figure>
