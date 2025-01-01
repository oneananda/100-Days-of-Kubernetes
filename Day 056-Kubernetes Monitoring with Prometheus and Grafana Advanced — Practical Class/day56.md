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
