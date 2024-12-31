---

## Day 55: Kubernetes Monitoring with Prometheus and Grafana — Practical Class

### 📘 Overview

Effective monitoring is essential for managing Kubernetes clusters. **Prometheus** is an open-source monitoring system that collects and stores metrics, while **Grafana** is a visualization tool used to analyze and display those metrics. Together, they provide a powerful monitoring solution for Kubernetes.

This session focuses on setting up Prometheus and Grafana, integrating them with Kubernetes, and creating dashboards to monitor cluster health.

---

### Objectives

1. Understand the purpose and functionality of Prometheus and Grafana in Kubernetes monitoring.
2. Install and configure Prometheus and Grafana in a Kubernetes cluster.
3. Collect metrics from Kubernetes components.
4. Visualize the metrics using Grafana dashboards.

---

### Key Concepts

1. **Prometheus**:
   - A time-series database that scrapes metrics from monitored services.
   - Uses a pull-based model for metric collection.
   - Provides a powerful query language (PromQL) for analyzing metrics.

2. **Grafana**:
   - A visualization tool for creating and sharing dashboards.
   - Supports various data sources, including Prometheus.
   - Enables alerting and collaboration on metrics.

3. **Kubernetes Metrics**:
   - Includes resource usage (CPU, memory, disk, etc.), pod health, node health, and more.
   - Metrics are exposed by Kubernetes components like the kubelet and cAdvisor.

4. **Prometheus Operator**:
   - Simplifies the deployment and management of Prometheus in Kubernetes.

---

### Practical Exercises

---

#### 1. Installing Prometheus and Grafana

##### Step 1: Deploy Prometheus
Use Helm to install the Prometheus Operator:
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack
```

Verify the installation:
```bash
kubectl get pods -n default
```

##### Step 2: Deploy Grafana
Grafana is included with the Prometheus Operator. Check the pod status:
```bash
kubectl get pods -l app.kubernetes.io/name=grafana
```

Retrieve the Grafana admin password:
```bash
kubectl get secret prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode
```

Access the Grafana dashboard:
1. Forward the Grafana port:
   ```bash
   kubectl port-forward svc/prometheus-grafana 3000:80
   ```
2. Open your browser and navigate to `http://localhost:3000`.
3. Log in with the username `admin` and the retrieved password.

---

#### 2. Configuring Prometheus to Collect Metrics

##### Step 1: Enable Node Exporter
Node Exporter is included with the Prometheus stack. Verify its pods:
```bash
kubectl get pods -l app.kubernetes.io/name=node-exporter
```

##### Step 2: Add Kubernetes Metrics
Edit the `prometheus.yaml` configuration to add Kubernetes components like kube-scheduler and kube-controller-manager as scrape targets.

---

#### 3. Visualizing Metrics with Grafana

##### Step 1: Add Prometheus as a Data Source
1. In Grafana, navigate to **Configuration** > **Data Sources**.
2. Click **Add Data Source** and select **Prometheus**.
3. Enter the Prometheus service URL (`http://prometheus-server`) and save.

##### Step 2: Import Predefined Dashboards
Grafana includes predefined dashboards for Kubernetes monitoring:
1. Go to **Dashboards** > **Import**.
2. Use the dashboard ID for Kubernetes monitoring (e.g., `6417`).
3. Link the dashboard to the Prometheus data source.

##### Step 3: Explore Metrics
Use the imported dashboards to monitor:
- Cluster resource usage
- Pod and node health
- Application performance

---

#### 4. Setting Alerts

##### Step 1: Create Alerts in Grafana
1. Navigate to a dashboard panel and select **Edit**.
2. Go to the **Alerts** tab and configure alert rules.
3. Set thresholds for metrics like CPU or memory usage.

##### Step 2: Test Alerts
Simulate a high-resource usage workload:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: resource-test
spec:
  replicas: 2
  selector:
    matchLabels:
      app: resource-test
  template:
    metadata:
      labels:
        app: resource-test
    spec:
      containers:
      - name: stress
        image: polinux/stress
        args:
        - "--cpu"
        - "2"
        - "--timeout"
        - "600s"
```

Apply the deployment:
```bash
kubectl apply -f resource-test.yaml
```

---

#### 5. Cleanup

Remove the test workload and the monitoring stack:
```bash
kubectl delete -f resource-test.yaml
helm uninstall prometheus
```

---

### Best Practices for Kubernetes Monitoring

1. **Use Predefined Dashboards**:
   - Start with community dashboards to save setup time.

2. **Optimize Scrape Intervals**:
   - Set appropriate scrape intervals to balance performance and granularity.

3. **Set Alerts Wisely**:
   - Avoid alert fatigue by configuring meaningful thresholds.

4. **Secure Access**:
   - Use authentication and encryption for Prometheus and Grafana.

5. **Monitor Long-Term Trends**:
   - Use Grafana to analyze historical data for capacity planning.

---
