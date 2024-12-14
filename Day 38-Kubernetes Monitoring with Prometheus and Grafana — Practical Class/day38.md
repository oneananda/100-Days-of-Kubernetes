---

## Day 38: Kubernetes Monitoring with Prometheus and Grafana — Practical Class

### 📘 Overview

Monitoring is essential for understanding the health, performance, and usage of Kubernetes clusters and workloads. **Prometheus** is a popular open-source monitoring tool that collects and stores metrics, while **Grafana** provides visualization capabilities for Prometheus metrics.

This practical session focuses on setting up Prometheus and Grafana to monitor Kubernetes clusters and workloads.

---


### Objectives

1. Understand how Prometheus and Grafana work in Kubernetes.
2. Deploy Prometheus and Grafana in a Kubernetes cluster.
3. Configure Prometheus to scrape metrics from the cluster.
4. Use Grafana to create dashboards and visualize metrics.

---

### Key Concepts

1. **Prometheus**:
   - Collects and stores time-series data.
   - Uses a pull-based model to scrape metrics from exporters and Kubernetes objects.

2. **Grafana**:
   - Provides dashboards and visualization tools for Prometheus metrics.
   - Integrates with Prometheus and other data sources.

3. **Node Exporter**:
   - Collects node-level metrics like CPU, memory, and disk usage.

4. **Kube-State-Metrics**:
   - Provides detailed metrics on Kubernetes resources like pods, deployments, and nodes.

---


### Practical Exercises

---

### 1. Deploying Prometheus

#### Step 1: Create a Namespace
```bash
kubectl create namespace monitoring
```

#### Step 2: Deploy Prometheus
Use the Prometheus Operator for easier management. Apply the following YAML to deploy Prometheus and related components:

```bash
kubectl apply -f https://github.com/prometheus-operator/prometheus-operator/releases/latest/download/bundle.yaml
```

Verify the deployment:
```bash
kubectl get pods -n monitoring
```

---

### 2. Deploying Grafana

#### Step 1: Deploy Grafana
Save the following YAML as `grafana-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana
        image: grafana/grafana:latest
        ports:
        - containerPort: 3000
          protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: grafana
  namespace: monitoring
spec:
  selector:
    app: grafana
  ports:
  - protocol: TCP
    port: 80
    targetPort: 3000
  type: NodePort
```

Apply the configuration:
```bash
kubectl apply -f grafana-deployment.yaml
```

Retrieve the NodePort to access Grafana:
```bash
kubectl get svc grafana -n monitoring
```

---

### 3. Configuring Prometheus

#### Step 1: Configure Prometheus to Scrape Kubernetes Metrics
Edit the Prometheus configuration to add Kubernetes scrape jobs. Save the following YAML as `prometheus-config.yaml`:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: prometheus
  namespace: monitoring
spec:
  serviceMonitorSelector:
    matchLabels:
      team: frontend
  replicas: 1
  additionalScrapeConfigs:
    name: additional-scrape-configs
    key: prometheus-additional.yaml
```

Apply the configuration:
```bash
kubectl apply -f prometheus-config.yaml
```

---

### 4. Adding Dashboards in Grafana

#### Step 1: Access Grafana
Open your browser and navigate to `http://<NodeIP>:<NodePort>`. Log in using the default credentials:
- Username: `admin`
- Password: `admin` (you will be prompted to change it).

#### Step 2: Add Prometheus as a Data Source
1. Navigate to **Configuration > Data Sources**.
2. Click **Add data source** and select **Prometheus**.
3. Enter the Prometheus service URL (e.g., `http://prometheus:9090`) and save.

#### Step 3: Import a Kubernetes Dashboard
1. Navigate to **Dashboard > Import**.
2. Use a community dashboard ID (e.g., `6417` for Kubernetes cluster monitoring).
3. Select the Prometheus data source and click **Import**.

---

### 5. Testing and Monitoring

#### View Metrics in Prometheus
Access the Prometheus UI:
```bash
kubectl port-forward svc/prometheus-operated -n monitoring 9090:9090
```
Open your browser at `http://localhost:9090`.

Run queries like:
```promql
kube_pod_info
node_memory_MemAvailable_bytes
```

#### View Dashboards in Grafana
Navigate through the imported Kubernetes dashboard in Grafana to observe metrics like:
- Node CPU and memory usage.
- Pod status and resource consumption.
- Cluster-wide resource utilization.

---

### 6. Cleanup

Remove all monitoring resources:
```bash
kubectl delete namespace monitoring
```

---

### Best Practices for Kubernetes Monitoring

1. **Set Alerts**:
   - Use Alertmanager with Prometheus to set up notifications for critical issues.

2. **Dashboards**:
   - Regularly update and customize Grafana dashboards for specific use cases.

3. **Scaling**:
   - Scale Prometheus and Grafana deployments for large clusters to handle more metrics.

4. **Storage Retention**:
   - Configure Prometheus for long-term storage of metrics if required.

5. **Security**:
   - Secure access to Grafana and Prometheus with authentication and authorization.

---


### 📝 Document Your Progress

In your `day38.md` file, record:
- Steps for deploying Prometheus and Grafana.
- Queries run in Prometheus and observations.
- Screenshots or notes on Grafana dashboards.
- Challenges faced and resolutions.

---

### 🎯 Outcome for Day 38

By the end of Day 38, you should:
1. Understand the purpose of Prometheus and Grafana in Kubernetes monitoring.
2. Deploy and configure Prometheus and Grafana.
3. Query metrics in Prometheus and visualize them in Grafana.
4. Apply best practices for monitoring Kubernetes clusters.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Monitoring](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-metrics-pipeline/)
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)
- [Kube-State-Metrics](https://github.com/kubernetes/kube-state-metrics)

---