# Failure

### Failure

Failure alerts indicate some kind of deviation in the system's configuration from it's desired state. For e.g., when replicas are configured in a redis database there must be at least one master instance. When there are none, redis is not operating as configured. These kind of problems are reported as failure alerts. Let's write an alert rule to report this failure.     &#x20;

```
# Redis Master Missing
# Note this covers both cluster mode and HA mode, thus we are counting by redis_mode
- alert: RedisMissingMaster
  expr: |-
    count by (job, service, redis_mode, namespace, asserts_env, asserts_site) (
      redis_instance_info{role="master"}
    ) == 0
  for: 1m
  labels:
    asserts_severity: critical
    asserts_entity_type: Service
    asserts_alert_category: failure
```

The `asserts_env, asserts_site, namespace, service` and `job` label have been explained earlier.  Similarly the `asserts_entity_type` has also been explained. To refresh, this will determine to which entity type is this alert associated in the visualization. We also have some new meta labels in this rule. Let's try to understand them.

**asserts\_severity**

This is used to indicate the severity of the problem as either `warning` or `critical`. This determines the color coding in the visualization. `warning` is shown in yellow and `critical` in red color.

**asserts\_alert\_category**

Asserts categorizes all alerts into the SAAFE model, i.e. Saturation, Amend (i.e. Configuration changes to the system), Anomaly, Failure and Error. The meta label `asserts_alert_category` is used to categorize this alert as a `failure`
