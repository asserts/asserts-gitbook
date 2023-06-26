# Amend

Asserts records amends such as new versions, config updates, scale up and scale down events by observing changes in metric label values and metric values. Amends are annotated as blue bars on Asserts Workbench.&#x20;

For example, a metric like `redis_instance_info` has a label `redis_version`  and the label\_value is tracked by Asserts to record new versions

```
- record: asserts:version:info
  expr: group by (namespace, job, service, asserts_env, asserts_site, asserts_source, version)
          (label_replace(redis_instance_info, "version", "$1", "redis_version", "(.+)"))
  labels:
    asserts_source: redis
    asserts_version_type: service
```
