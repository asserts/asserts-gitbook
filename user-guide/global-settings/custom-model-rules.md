# Custom Model Rules

A set of model rules defines Asserts' entity model. Thus, we can extend the model by including more custom rules. There are two model rules: entity rules and relationship rules, and both only rely on Prometheus metrics data. Asserts runs the specified queries and uses the context information carried in labels to build entities and relationships.

Our default entities rules are carefully curated to cover most scenarios, so we usually do not expect customers to supply additional rules. If you still want to augment the model, you can add your own model rules by uploading a rule file.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Quite often, custom rules are about relationships between services. To eliminate the management hassle of custom relationship rules, Asserts platform supports service calls defined by an `asserts:relation:calls` metric. For example, customers can add the following recording rule indicating that an `api-server` service calls a `backend-server` service.

```yaml
- record: asserts:relation:calls
  expr: 1
  labels:
    job: api-server
    dst_job: backend-server
    namespace: api_namespace
    dst_namespace: backend_namespace
    asserts_env: dev
    asserts_site: dev
```

Note this rule is a custom metrics, thus not a custom model rule. It should be added in the [Assertion Management#addition section](../assertion-management.md#howassertsworks-wip-additions).
