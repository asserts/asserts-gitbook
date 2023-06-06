# RabbitMQ

### Setup

#### For versions 3.8.0+&#x20;

Refer to the [instructions](https://www.rabbitmq.com/prometheus.html#installation) to enable the `rabbitmq_prometheus` plugin. Once the plugin is enabled, the metrics will be available on port `15692` at the `/metrics` endpoint.&#x20;

#### For versions < 3.8.0

We recommend the [rabbitmq exporter](https://github.com/kbudde/rabbitmq\_exporter) that's also available as a [Prometheus community helm chart](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-rabbitmq-exporter).

### Metrics and Key Performance Indicators (KPIs)

<table><thead><tr><th width="388">Metrics</th><th>KPI</th></tr></thead><tbody><tr><td><p><strong>Requests</strong></p><p>Total Messages received</p><pre><code>rabbitmq_channel_messages_published_total
</code></pre><p>Total Outgoing Messages</p><pre><code>rabbitmq_channel_messages_delivered_total 
+ rabbitmq_channel_messages_delivered_ack_total 
+ rabbitmq_channel_messages_redelivered_total 
+ rabbitmq_channel_get_total 
+ rabbitmq_channel_get_ack_total
</code></pre><p>Total Messages routed</p><pre><code>rabbitmq_queue_messages_published_total
</code></pre></td><td><strong>Request Rate</strong> <code>rate(</code>rabbitmq_channel_messages_published_total<code>[5m])</code></td></tr><tr><td><p><strong>Errors</strong></p><p>Total messages not routed. Includes dropped and returned.</p><pre><code>rabbitmq_channel_messages_unroutable_dropped_total 
+ rabbitmq_channel_messages_unroutable_returned_total
</code></pre></td><td><p><strong>Error Ratio</strong> </p><p>Error Rate / Request Rate of Total Messages received</p></td></tr><tr><td><p><strong>Memory</strong> </p><pre><code>rabbitmq_process_resident_memory_bytes
rabbitmq_resident_memory_limit_bytes
</code></pre></td><td><p><strong>Memory Utilization</strong> </p><pre><code>rabbitmq_process_resident_memory_bytes
/ rabbitmq_resident_memory_limit_bytes
</code></pre></td></tr><tr><td><p><strong>File Descriptors</strong> </p><pre><code>rabbitmq_process_open_fds
rabbitmq_process_max_fds
</code></pre></td><td><p><strong>File Descriptors Utilization</strong> </p><pre><code>rabbitmq_process_open_fds
/ rabbitmq_process_max_fds
</code></pre></td></tr><tr><td><p><strong>Open TCP Sockets</strong> </p><pre><code>rabbitmq_process_open_tcp_sockets
rabbitmq_process_max_tcp_sockets
</code></pre></td><td><p><strong>TCP Sockets Utilization</strong> </p><pre><code>rabbitmq_process_open_tcp_sockets
/ rabbitmq_process_max_tcp_sockets
</code></pre></td></tr><tr><td><p><strong>Network Bytes Transmitted/Received</strong> </p><pre><code>rabbitmq_connection_incoming_bytes_total
rabbitmq_connection_outgoing_bytes_total
</code></pre></td><td><p><strong>Transmission Rate</strong> </p><pre><code>rate(rabbitmq_connection_incoming_bytes_total[5m])

rate(rabbitmq_connection_outgoing_bytes_total[5m])
</code></pre></td></tr></tbody></table>

### Dashboard
