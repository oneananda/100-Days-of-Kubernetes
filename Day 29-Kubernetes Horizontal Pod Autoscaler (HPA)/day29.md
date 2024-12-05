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