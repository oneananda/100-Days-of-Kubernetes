---

## Day 39: Kubernetes Horizontal Pod Autoscaling (HPA) — Practical Class

### 📘 Overview

Scaling applications in Kubernetes is crucial to handle varying workloads efficiently. **Horizontal Pod Autoscaler (HPA)** automatically adjusts the number of pod replicas in a deployment, replica set, or stateful set based on observed CPU/memory utilization or custom metrics.

This session focuses on setting up and configuring HPA to achieve dynamic scaling of pods.

---


### Objectives

1. Understand the concept and importance of HPA in Kubernetes.
2. Deploy an application with HPA enabled.
3. Configure HPA using resource-based and custom metrics.
4. Test HPA behavior under varying loads.

---

### Key Concepts

1. **Horizontal Pod Autoscaler (HPA)**:
   - Adjusts the number of replicas automatically based on resource utilization.
   - Uses the Kubernetes Metrics Server for real-time metrics.

2. **Metrics Server**:
   - A lightweight component that collects resource metrics from Kubernetes nodes and pods.
   - Provides CPU and memory utilization data to the HPA.

3. **Scaling Thresholds**:
   - The target resource utilization percentage for triggering scaling actions.

---
