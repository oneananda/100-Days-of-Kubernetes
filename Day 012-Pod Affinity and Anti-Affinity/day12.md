---

## Day 12: Pod Affinity and Anti-Affinity — Controlling Pod Placement

### 📘 Overview

**Pod Affinity and Anti-Affinity** are advanced scheduling techniques in Kubernetes that let you control how Pods are co-located or spread across nodes. This is especially useful for optimizing performance, improving fault tolerance, and managing workloads efficiently.

---

### What is Pod Affinity and Anti-Affinity?

- **Pod Affinity** ensures that Pods are scheduled close to each other, improving locality for data sharing or inter-application communication.
- **Pod Anti-Affinity** ensures that Pods are scheduled away from each other, which helps spread workloads and improve fault tolerance.

---