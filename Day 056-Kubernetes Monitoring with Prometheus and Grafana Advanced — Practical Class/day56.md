﻿---

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

#### 2. Building Advanced Grafana Dashboards

##### Step 1: Create a Custom Dashboard
1. Open Grafana (`http://localhost:3000`) and navigate to **Create** > **Dashboard**.
2. Add a new panel and use a PromQL query for metrics visualization.
3. Customize the panel display options (e.g., graph type, thresholds).

##### Step 2: Add Variables for Flexibility
1. Go to the **Dashboard Settings** > **Variables**.
2. Add a variable for namespaces:
   - Query: `label_values(kube_pod_info, namespace)`
   - Use the variable in panels to filter data by namespace.

##### Step 3: Create a Dynamic Dashboard
Combine multiple panels for a comprehensive view:
- Node resource usage.
- Pod health and availability.
- Custom application metrics (e.g., request latency).

---

#### 3. Adding External Exporters

##### Step 1: Install an Exporter (e.g., MySQL Exporter)
Deploy the MySQL exporter:
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install mysql-exporter prometheus-community/prometheus-mysql-exporter --set mysql.host=<db-host> --set mysql.user=<user> --set mysql.pass=<password>
```

Verify that the exporter is running:
```bash
kubectl get pods -l app=mysql-exporter
```

##### Step 2: Configure Prometheus to Scrape Metrics
Edit the Prometheus configuration to add the exporter as a target:
```yaml
- job_name: 'mysql-exporter'
  static_configs:
  - targets: ['<mysql-exporter-service>:9104']
```
Reload Prometheus to apply the changes.

---

#### 4. Advanced Alerting with Notifications

##### Step 1: Configure Prometheus Alerting Rules
Add alerting rules in the Prometheus configuration:
```yaml
groups:
- name: advanced-alerts
  rules:
  - alert: HighCPUUsage
    expr: avg(rate(node_cpu_seconds_total{mode!="idle"}[5m])) > 0.8
    for: 1m
    labels:
      severity: warning
    annotations:
      summary: "High CPU usage detected"
      description: "CPU usage is above 80% for more than 1 minute."
```
Reload Prometheus to apply the rules.

##### Step 2: Integrate Notification Channels in Grafana
1. Go to Grafana **Alerting** > **Notification Channels**.
2. Add a channel for Slack:
   - Name: `Slack Alerts`
   - Type: `Slack`
   - URL: `<Slack Webhook URL>`
3. Assign the notification channel to alerts.

---

#### 5. Using Prometheus with Long-Term Storage

Prometheus metrics are ephemeral. Integrate with a long-term storage system like Thanos or Cortex for historical analysis:
- Install Thanos for Prometheus:
  ```bash
  helm install thanos prometheus-community/prometheus-thanos
  ```
- Configure Thanos to store metrics in an object storage backend (e.g., AWS S3, GCS).

---

#### 6. Cleanup

Remove the deployed resources:
```bash
helm uninstall prometheus
helm uninstall mysql-exporter
helm uninstall thanos
```

---

### Best Practices for Advanced Monitoring

1. **Optimize Query Performance**:
   - Use efficient PromQL queries to minimize Prometheus load.

2. **Use Templated Dashboards**:
   - Create reusable templates for similar environments.

3. **Combine Alerts Across Tools**:
   - Use both Prometheus and Grafana alerts for comprehensive coverage.

4. **Automate Exporter Configuration**:
   - Use Helm or Kubernetes Operators to manage exporters.

5. **Store Metrics Long-Term**:
   - Leverage tools like Thanos or Cortex for historical metrics and large-scale monitoring.

---

### 📝 Document Your Progress

In your `day56.md` file, include:
- Custom PromQL queries you created.
- Screenshots of advanced Grafana dashboards.
- Notes on using external exporters.
- Challenges encountered and solutions applied.
- Details on alerting configuration and integrations.

---

### 🎯 Outcome for Day 56

By the end of this session, you will:
1. Write custom PromQL queries for detailed monitoring.
2. Build advanced Grafana dashboards with variables and templates.
3. Extend monitoring with external exporters.
4. Configure and test advanced alerts integrated with notification systems.

---

### 🔗 Additional Resources

- [Prometheus Documentation: PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/)
- [Grafana Documentation: Dashboard Variables](https://grafana.com/docs/grafana/latest/variables/)
- [Kubernetes Monitoring with External Exporters](https://prometheus.io/docs/instrumenting/exporters/)
- [Thanos Documentation](https://thanos.io/)

---