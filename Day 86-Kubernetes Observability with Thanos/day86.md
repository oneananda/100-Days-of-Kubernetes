---

## Day 86: Kubernetes Observability with Thanos  

### 📘 Overview  

**Observability** is crucial for managing and troubleshooting Kubernetes clusters. **Thanos** extends **Prometheus** by providing long-term metric storage, global querying, and high availability across multiple clusters. This session focuses on understanding Thanos, configuring it for long-term storage, and enabling multi-cluster observability with **Thanos Querier**.

---


### Objectives  

1. Understand **Thanos** and how it enhances Prometheus for large-scale observability.  
2. Set up **Thanos components** for long-term storage and high availability.  
3. Configure **Thanos Querier** to enable **multi-cluster metric aggregation**.  

---

### Key Concepts  

1. **Why Use Thanos?**  
   - Scales Prometheus horizontally by enabling multiple instances to work together.  
   - Provides **long-term storage** for metrics, overcoming Prometheus’s retention limits.  
   - Enables **multi-cluster observability**, allowing centralized monitoring of different Kubernetes environments.  

2. **Thanos Components:**  
   - **Thanos Sidecar:** Attaches to Prometheus to enable external storage and query federation.  
   - **Thanos Store:** Retrieves historical metrics from long-term storage (S3, GCS, etc.).  
   - **Thanos Compactor:** Optimizes and deduplicates stored metrics for efficient querying.  
   - **Thanos Query:** A single-pane-of-glass UI for querying Prometheus instances across multiple clusters.  

3. **Long-Term Storage:**  
   - Stores metrics in **object storage** (e.g., AWS S3, Google Cloud Storage) for infinite retention.  

---


### Practical Exercises: Setting Up Thanos for Observability  

---

#### 1. Installing Prometheus with Thanos Sidecar  

##### Step 1: Deploy Prometheus in Kubernetes  
If you don’t already have Prometheus installed, deploy it using Helm:  
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace
```

Verify installation:  
```bash
kubectl get pods -n monitoring
```

##### Step 2: Deploy Thanos Sidecar  
Modify the Prometheus deployment to include **Thanos Sidecar**:  
```yaml
containers:
  - name: prometheus
    image: prom/prometheus
    args:
      - "--storage.tsdb.path=/prometheus"
      - "--config.file=/etc/prometheus/prometheus.yml"
      - "--web.enable-lifecycle"
    ports:
      - containerPort: 9090

  - name: thanos-sidecar
    image: quay.io/thanos/thanos:v0.30.1
    args:
      - "sidecar"
      - "--prometheus.url=http://localhost:9090"
      - "--grpc-address=0.0.0.0:10901"
      - "--objstore.config-file=/etc/thanos/objstore.yml"
    ports:
      - containerPort: 10901
    volumeMounts:
      - mountPath: /etc/thanos
        name: thanos-config
```

Apply the updated deployment:  
```bash
kubectl apply -f prometheus-with-thanos.yaml
```

---

#### 2. Configuring Long-Term Storage  

##### Step 1: Create an Object Storage Bucket  
For AWS S3, create a bucket for storing Prometheus metrics:  
```bash
aws s3 mb s3://thanos-metrics-bucket --region us-east-1
```

##### Step 2: Configure Storage Backend  
Create a Thanos storage configuration file (`objstore.yml`):  
```yaml
type: S3
config:
  bucket: "thanos-metrics-bucket"
  endpoint: "s3.amazonaws.com"
  access_key: "<AWS_ACCESS_KEY>"
  secret_key: "<AWS_SECRET_KEY>"
```

Mount the configuration file into the Thanos Sidecar pod.

---

#### 3. Setting Up Thanos Query for Multi-Cluster Observability  

##### Step 1: Deploy Thanos Query  
Create a Thanos Query deployment that connects to multiple Prometheus instances:  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: thanos-query
spec:
  replicas: 1
  selector:
    matchLabels:
      app: thanos-query
  template:
    metadata:
      labels:
        app: thanos-query
    spec:
      containers:
        - name: thanos-query
          image: quay.io/thanos/thanos:v0.30.1
          args:
            - "query"
            - "--grpc-address=0.0.0.0:10901"
            - "--http-address=0.0.0.0:9090"
            - "--store=thanos-sidecar.monitoring.svc:10901" # Add more store endpoints for multi-cluster
          ports:
            - containerPort: 9090
```

Apply the deployment:  
```bash
kubectl apply -f thanos-query.yaml
```

##### Step 2: Access Thanos Query UI  
Expose the Thanos Query service:  
```yaml
apiVersion: v1
kind: Service
metadata:
  name: thanos-query
spec:
  type: NodePort
  ports:
    - port: 9090
      targetPort: 9090
  selector:
    app: thanos-query
```

Get the service URL:  
```bash
kubectl get svc thanos-query
```

Access Thanos Query at `http://<NODE_IP>:<NODE_PORT>` and explore metrics from multiple clusters.

---


### Best Practices for Observability with Thanos  

1. **Use Object Storage for Long-Term Retention:**  
   - Store historical metrics in S3, GCS, or MinIO for infinite retention.  

2. **Enable Cross-Cluster Observability:**  
   - Use **Thanos Query** to federate metrics from multiple Kubernetes clusters.  

3. **Optimize Metrics Storage:**  
   - Enable **Thanos Compactor** to reduce storage costs and improve query performance.  

4. **Secure Data Access:**  
   - Use IAM policies and role-based access control (RBAC) to secure object storage and Thanos components.  

5. **Monitor Thanos Performance:**  
   - Use **Grafana dashboards** to visualize Thanos metrics and detect issues early.  

---


### 📝 Document Your Progress  

In your `day86.md` file, include:  
- Steps for installing Thanos and configuring long-term storage.  
- Observations on multi-cluster observability using Thanos Querier.  
- Insights into how Thanos improves Prometheus scalability.  

---

### 🎯 Outcome for Day 86  

By the end of this session, you will:  
1. Deploy Thanos to extend Prometheus with long-term storage.  
2. Configure Thanos Querier for **multi-cluster observability**.  
3. Implement best practices for **scalable monitoring in Kubernetes**.  

---

### 🔗 Additional Resources  

- [Thanos Documentation](https://thanos.io/tip/thanos/getting-started.md/)  
- [Prometheus + Thanos Setup](https://prometheus.io/docs/prometheus/latest/storage/)  
- [Kubernetes Monitoring with Thanos](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-metrics-pipeline/)  
- [Grafana Thanos Integration](https://grafana.com/docs/grafana/latest/datasources/thanos/)  

---