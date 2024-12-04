---

## Day 28: Kubernetes Node Affinity and Anti-Affinity — Optimizing Pod Placement

### 📘 Overview

**Node Affinity and Anti-Affinity** in Kubernetes allow you to control how pods are scheduled onto nodes based on specific node attributes. These mechanisms help optimize workload placement, resource utilization, and fault tolerance.

---


### What is Node Affinity and Anti-Affinity?

1. **Node Affinity**:
   - Ensures pods are scheduled on nodes that match specific criteria.
   - Similar to nodeSelector but more flexible, supporting expressions and operators.

2. **Node Anti-Affinity**:
   - Prevents pods from being scheduled on specific nodes.

3. **Affinity Types**:
   - **Required (Hard)**: Pods must be scheduled only on matching nodes.
   - **Preferred (Soft)**: Pods are ideally scheduled on matching nodes but can fall back to other nodes.

---

### Key Concepts

1. **Node Labels**:
   - Affinity rules rely on node labels for matching.
   - Example: `kubernetes.io/role=worker`

2. **Expressions and Operators**:
   - Operators like `In`, `NotIn`, `Exists`, `DoesNotExist` allow complex matching criteria.

3. **Use Cases**:
   - Scheduling workloads near specific resources (e.g., GPUs).
   - Isolating sensitive workloads on dedicated nodes.
   - Distributing workloads across fault domains.

---