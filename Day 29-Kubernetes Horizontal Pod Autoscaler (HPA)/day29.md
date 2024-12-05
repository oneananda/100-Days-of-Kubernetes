---

## Day 29: Kubernetes Horizontal Pod Autoscaler (HPA) — Scaling Workloads Dynamically

### 📘 Overview

**Horizontal Pod Autoscaler (HPA)** in Kubernetes dynamically adjusts the number of pod replicas in a Deployment, StatefulSet, or ReplicaSet based on observed CPU, memory, or custom metrics. HPA ensures optimal resource utilization and cost efficiency by scaling workloads based on demand.

---

### What is Horizontal Pod Autoscaler?

1. **Purpose**:
   - Automatically scale applications horizontally (increase/decrease replicas) based on metrics.

2. **Supported Metrics**:
   - **Resource Metrics**: CPU and memory usage.
   - **Custom Metrics**: Application-specific metrics like request latency or queue depth.
   - **External Metrics**: Metrics from external systems, e.g., CloudWatch or Prometheus.

3. **Scaling Criteria**:
   - HPA scales up or down when the metrics exceed or fall below the defined thresholds.

---

### Key Concepts

1. **HPA Controller**:
   - Continuously monitors the metrics of target pods and adjusts replica counts as needed.

2. **Metrics Server**:
   - Required for HPA to fetch resource metrics (e.g., CPU, memory).

3. **Cool-Down Period**:
   - Prevents frequent scaling by introducing a delay between scaling actions.

---


### Hands-On with Horizontal Pod Autoscaler

---

### 1. Prerequisites

#### Install the Metrics Server:
Ensure the Metrics Server is deployed in your cluster:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Verify the Metrics Server:
```bash
kubectl get apiservices | grep metrics
kubectl top nodes
```

---

### 2. Deploy a Sample Application

#### Create a Deployment:
Save the following as `hpa-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  replicas: 1
  selector:
    matchLabels:
      app: php-apache
  template:
    metadata:
      labels:
        app: php-apache
    spec:
      containers:
      - name: php-apache
        image: k8s.gcr.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 200m
          limits:
            cpu: 500m
```

Apply the Deployment:
```bash
kubectl apply -f hpa-deployment.yaml
```

Verify Deployment:
```bash
kubectl get deployment php-apache
```

---

### 3. Create a Horizontal Pod Autoscaler

#### Define an HPA:
```bash
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
```

#### Verify the HPA:
```bash
kubectl get hpa
kubectl describe hpa php-apache
```

---

### 4. Simulate Load

#### Generate Load Using a Load Testing Tool:
Run a load generator like `kubectl run` with a continuous curl command:

```bash
kubectl run -i --tty load-generator --image=busybox --restart=Never -- /bin/sh -c "while true; do wget -q -O- http://php-apache; done"
```

#### Observe Scaling:
Monitor the HPA and pods:

```bash
kubectl get hpa
kubectl get pods -o wide
```

---

### 5. Custom Metrics with HPA (Optional)

#### Deploy Prometheus Adapter:
Use the Prometheus Adapter to expose custom metrics to the HPA.

#### Define a Custom HPA:
Save the following YAML as `custom-hpa.yaml`:

```yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: custom-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: php-apache
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
  - type: External
    external:
      metricName: queue_depth
      targetValue: 100
```

Apply the Custom HPA:
```bash
kubectl apply -f custom-hpa.yaml
```

---

### Best Practices for HPA

1. **Use Appropriate Metrics**:
   - Prefer resource metrics for general scaling.
   - Use custom or external metrics for specific application behaviors.

2. **Set Realistic Thresholds**:
   - Avoid overly aggressive or conservative thresholds to prevent instability.

3. **Monitor Scaling Behavior**:
   - Use tools like Prometheus and Grafana to visualize HPA activity and performance.

4. **Test Scaling Scenarios**:
   - Simulate both high and low loads to ensure HPA responds as expected.

---

### 📝 Document Your Progress

In your `day29.md` file, record:
- Steps for deploying the Metrics Server and HPA.
- YAML configurations for deployments and autoscalers.
- Observations during scaling and load testing.
- Insights on optimizing scaling behavior.

---

### 🎯 Outcome for Day 29

By the end of Day 29, you should:
1. Understand the role of Horizontal Pod Autoscaler in Kubernetes.
2. Set up and configure HPA for resource-based and custom metrics.
3. Simulate load and observe dynamic scaling in action.
4. Apply best practices for effective scaling of Kubernetes workloads.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: HPA](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [Autoscaling with Custom Metrics](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-custom-metrics)

---