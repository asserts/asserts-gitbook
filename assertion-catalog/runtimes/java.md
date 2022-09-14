# Java

### Setup

The following are the two popular exporters for Java JVM Metrics [JMX Exporter](https://github.com/prometheus/jmx\_exporter) or [Micrometer Prometheus Exporter](https://micrometer.io/docs/registry/prometheus). The Micrometer exporter provides more information and is the recommended option.

### Metrics and Key Performance Indicators (KPI)s

| **Metric**                                              | **KPI**                                                                                         |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| <p>GC Count</p><p>jvm_gc_pause_seconds_count</p>        | <p>Rate of GC Count</p><p>rate(jvm_gc_pause_seconds_count{action="end of major GC"}[2m])</p>    |
| <p>GC Pause Time</p><p>jvm_gc_pause_seconds_sum</p>     | <p>Rate of GC Pause Time</p><p>rate(jvm_gc_pause_seconds_sum{action="end of major GC"}[2m])</p> |
| <p>Live Thread Count</p><p>jvm_threads_live_threads</p> | <p>Live thread count normalized against a threshold</p><p>jvm_threads_live_threads / 200</p>    |

### Alerts

| **KPI**               | **Alert**                       |
| --------------------- | ------------------------------- |
| Rate of GC Count      | JvmMajorGCCountHigh             |
| Rate of GC Pause Time | JvmMajorGCTimeHigh              |
| Live Thread Count     | Saturation on jvm:live\_threads |

### Dashboard

#### CPU

* system\_cpu\_usage
* process\_cpu\_usage
* system\_cpu\_count
* system\_load\_average\_1m

#### Heap Memory

The following heap metrics are available for Eden Space, Survivor Space and Tenured Generation

* jvm\_memory\_used\_bytes{area="heap"}
* jvm\_memory\_committed\_bytes{area="heap"}
* jvm\_memory\_max\_bytes{area="heap"}

#### Non Heap Memory

The following non heap metrics are available for Metaspace, Compressed Class Space and Code Cache

* jvm\_memory\_used\_bytes{area="nonheap"}
* jvm\_memory\_committed\_bytes{area="nonheap"}
* jvm\_memory\_max\_bytes{area="nonheap"}

#### Buffer Pools

The following metrics are available for Direct and Mapped buffers

* jvm\_buffer\_total\_capacity\_bytes
* jvm\_buffer\_memory\_used\_bytes
* jvm\_buffer\_count\_buffers

#### Threads

* jvm\_threads\_live\_threads
* jvm\_threads\_daemon\_threads
* jvm\_threads\_peak\_threads
* process\_threads
* jvm\_threads\_states\_threads

#### File Descriptors

* process\_files\_open\_files
* process\_files\_max\_files

#### Classes Loaded

* jvm\_classes\_loaded\_classes

![](<../../.gitbook/assets/image (21).png>)
