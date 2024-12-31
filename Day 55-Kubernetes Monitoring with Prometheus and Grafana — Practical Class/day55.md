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
