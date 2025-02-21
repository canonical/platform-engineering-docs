# Create Dashboard

This guide provides a concise reference for creating a service health dashboard based on the four key monitoring signals: Latency, Errors, Traffic and Saturation.

Each panel should be created as a library panel so it can be easily imported into a general dashboard (see [Add to Platform Engineering Dashboard](add-to-pe-dashboard)). Additionally, a `juju_model` variable should be added to allow filtering per model.

---

## Latency

Latency measures how long requests take to be processed. It’s important to distinguish between the latency of successful requests and the latency of failed requests.

What was considered for Synapse: 95% of requests were completed in this time or less.

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

Errors represent failed requests. This panel should track the current rate of failed requests per minute, grouped by HTTP status code. Monitoring error rates helps in identifying system issues and response failures.

What was considered for Synapse: Current rate of failed requests per minute, based on the last 5 minutes, grouped by error code.

### Query

```promql
sum by(code) (rate(synapse_http_server_responses_total{code=~"5..", juju_model=~"$synapse_model"}[5m]) * 60)
```

---

## Traffic

Traffic reflects the volume of incoming requests. This panel should display the total number of requests per minute, helping to assess demand and system load.

What was considered for Synapse: Total number of requests per minute over the last 5 minutes.

### Query

```promql
sum(rate(synapse_http_server_responses_total{juju_model=~"$synapse_model"}[5m]) * 60)
```

---

## Saturation

Saturation measures how much of the system’s resources are being used, such as CPU and memory. This panel should identify the unit with the highest resource consumption.

Synapse architecture consists of a main unit and multiple worker units. Typically, the main unit consumes the most resources. Since Prometheus metrics do not explicitly identify the main unit, the panel assumes it is the highest CPU and memory consumer and displays only that unit.

What was considered for Synapse: CPU and memory usage of the most resource-consuming unit over the last 5 minutes.

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
- The `juju_model` variable must be added to allow dynamic filtering by model.
- Use appropriate units in Grafana (e.g., percent for CPU, bytes for memory, requests per minute for traffic)

## Reference

Descriptions extracted from [Google SRE book](https://sre.google/sre-book/monitoring-distributed-systems/#xref_monitoring_golden-signals).

