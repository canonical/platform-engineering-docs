# Create Dashboard

This guide provides a concise reference for creating a service health dashboard based on the four key monitoring signals: Latency, Errors, Traffic and Saturation.

Each panel should be created as a library panel so it can be easily imported into a general dashboard (see [Add to Platform Engineering Dashboard](add-to-pe-dashboard)). Additionally, a `charmsname_model` variable should be added to allow filtering per model.

The variable will then be created while following the [Add to Platform Engineering Dashboard](add-to-pe-dashboard) instructions.

Create a panel for each signal considering the following information and examples.

Refer to the [Synapse Operator charm](https://github.com/canonical/synapse-operator/pull/702) for an example on how the `synapse_model`variable is used as well as how the panels should look like.

---

## Latency

Latency measures how long an operation takes to complete. Depending on the application, this could be the time to process a request, complete a transaction, finish a job, or execute a process. It’s important to distinguish between the latency of successful and failed operations.

What was considered for Synapse: 95% of requests were completed in the time displayed on the panel or less.

### Queries

Successful requests:

```promql
histogram_quantile(0.95, sum(rate(synapse_http_server_response_time_seconds_bucket{code=~"2..", juju_model=~"$synapse_model"}[5m])) by (le))
```

Failed requests:

```promql
histogram_quantile(0.95, sum(rate(synapse_http_server_response_time_seconds_bucket{code!~"2..", juju_model=~"$synapse_model"}[5m])) by (le))
```

---

## Errors

Errors represent failed operations across different system components. Monitoring error rates helps in identifying issues within web applications, databases, task runners, and other services, allowing for early detection of system failures or performance degradation.

What was considered for Synapse: Current rate of failed requests per minute, based on the last 5 minutes, grouped by error code.

### Query

```promql
sum by(code) (rate(synapse_http_server_responses_total{code=~"5..", juju_model=~"$synapse_model"}[5m]) * 60)
```

---

## Traffic

Traffic reflects the volume of operations. This panel should display the total number of operations per minute—such as requests for a web application, transactions for a database, or jobs for a task runner—helping to assess demand and system load.

What was considered for Synapse: Total number of requests per minute over the last 5 minutes.

### Query

```promql
sum(rate(synapse_http_server_responses_total{juju_model=~"$synapse_model"}[5m]) * 60)
```

---

## Saturation

Saturation measures how much of the system's resources are being utilized, including CPU, memory, disk I/O, and other critical resources across various components like databases, task runners, and other services, indicating when the system is nearing its capacity limits.

What was considered for Synapse: CPU and memory usage of the most resource-consuming unit over the last 5 minutes.

Synapse architecture consists of a main unit and multiple worker units. Typically, the main unit consumes the most resources. Since Prometheus metrics do not explicitly identify the main unit, the panel assumes it is the highest CPU and memory consumer and displays only that unit.

### Queries

**CPU Usage (%):**

```promql
max(rate(process_cpu_seconds_total{juju_unit=~"synapse.*",job=~".*synapse_application.*", juju_model=~"$synapse_model"}[2m]))
```

**Memory Usage (bytes):**

```promql
max(jemalloc_stats_app_memory_bytes{juju_unit=~"synapse.*",job=~".*synapse_application.*",juju_model=~"$synapse_model"})
```

---

## Additional Notes

- Each panel should be configured as a library panel for easy reuse across dashboards.
- The examples focus on Synapse, a chat/web application, but the Four Golden Signals can be adapted to any application. For example, a database might track the number of transactions, while Jenkins could monitor job executions.
- Use appropriate units in Grafana (e.g., percent for CPU, bytes for memory, requests per minute for traffic)
- Panels can be created using Loki as a data source, not just Prometheus.

## Reference

Descriptions extracted from [Google SRE book](https://sre.google/sre-book/monitoring-distributed-systems/#xref_monitoring_golden-signals).

