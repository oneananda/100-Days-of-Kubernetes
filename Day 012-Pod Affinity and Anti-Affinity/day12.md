---

## Day 12: Pod Affinity and Anti-Affinity — Controlling Pod Placement

### 📘 Overview

**Pod Affinity and Anti-Affinity** are advanced scheduling techniques in Kubernetes that let you control how Pods are co-located or spread across nodes. This is especially useful for optimizing performance, improving fault tolerance, and managing workloads efficiently.

---

### What is Pod Affinity and Anti-Affinity?

- **Pod Affinity** ensures that Pods are scheduled close to each other, improving locality for data sharing or inter-application communication.
- **Pod Anti-Affinity** ensures that Pods are scheduled away from each other, which helps spread workloads and improve fault tolerance.

---

### Key Features

1. **Topology Awareness**: Pods can be scheduled based on node labels, such as `kubernetes.io/hostname`.
2. **Hard and Soft Rules**:
   - **RequiredDuringSchedulingIgnoredDuringExecution**: Rules that must be satisfied for scheduling.
   - **PreferredDuringSchedulingIgnoredDuringExecution**: Rules that the scheduler tries to satisfy but are not mandatory.
3. **Use Cases**:
   - Pod Affinity: Databases with primary and replica Pods for better performance.
   - Pod Anti-Affinity: Distributed systems requiring Pods to run on different nodes for reliability.

---

### Hands-On with Pod Affinity and Anti-Affinity

Let’s explore how to implement Pod Affinity and Anti-Affinity.

---


