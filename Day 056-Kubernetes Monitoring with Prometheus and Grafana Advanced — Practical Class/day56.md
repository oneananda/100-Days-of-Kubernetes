---

## Day 56: Kubernetes Monitoring with Prometheus and Grafana Advanced — Practical Class

### 📘 Overview

This session dives into advanced configurations and use cases for **Prometheus** and **Grafana** in Kubernetes monitoring. You will learn to create custom dashboards, configure advanced alerting rules, and integrate external exporters for in-depth monitoring. This advanced class also covers PromQL for querying metrics and integrating Grafana with external notification systems.

---

### Objectives

1. Create custom Prometheus queries using PromQL for detailed insights.
2. Develop advanced Grafana dashboards tailored to specific use cases.
3. Configure external exporters for extended monitoring (e.g., databases, applications).
4. Set up advanced alerting and integrate with notification channels like Slack or email.

---

### Key Concepts

1. **PromQL (Prometheus Query Language)**:
   - A flexible query language for extracting and aggregating metrics.
   - Enables advanced analysis of Kubernetes metrics.

2. **Grafana Dashboard Customization**:
   - Build dynamic dashboards with templating.
   - Use variables for flexibility across environments.

3. **External Exporters**:
   - Extend Prometheus with exporters like:
     - Node Exporter (default for system metrics)
     - Database Exporters (e.g., MySQL, PostgreSQL)
     - Application-specific Exporters (e.g., Redis, NGINX)

4. **Alerting Integrations**:
   - Connect Grafana or Prometheus alerts to external systems such as Slack, PagerDuty, or email.

---

### Practical Exercises

---

#### 1. Creating Custom PromQL Queries

##### Step 1: Explore Existing Metrics
Access the Prometheus UI:
```bash
kubectl port-forward svc/prometheus-server 9090:80
```

Open `http://localhost:9090` in a browser and explore the `Targets` and `Graph` tabs.

##### Step 2: Write Custom Queries
Use PromQL to create advanced queries:
- Monitor average CPU usage across all nodes:
  ```promql
  avg(rate(node_cpu_seconds_total{mode!="idle"}[5m]))
  ```
- Identify memory usage for a specific pod:
  ```promql
  sum(container_memory_usage_bytes{pod="example-pod"})
  ```
- Track the number of active pods per namespace:
  ```promql
  count(kube_pod_info) by (namespace)
  ```

---
