---

## Day 14: StatefulSets — Managing Stateful Applications in Kubernetes

### 📘 Overview

While **Deployments** and **ReplicaSets** are excellent for stateless applications, **StatefulSets** are designed to manage stateful applications that require stable identities, persistent storage, and ordered deployment. StatefulSets ensure that Pods are created, deleted, and scaled in a controlled manner.

---

### What is a StatefulSet?

- A **StatefulSet** is a Kubernetes workload API object that manages stateful applications.
- It ensures:
  1. Stable, unique network identifiers for each Pod (e.g., `pod-0`, `pod-1`).
  2. Persistent storage with volume claims associated with each Pod.
  3. Ordered deployment, scaling, and termination.

---

### Key Features of StatefulSets

1. **Stable Pod Identifiers**: Each Pod gets a predictable hostname.
2. **Ordered Scaling**: Pods are created and terminated in sequence.
3. **Persistent Storage**: Pods retain data even after being recreated.
4. **Use Cases**:
   - Databases like MySQL, MongoDB.
   - Distributed systems like Kafka, Zookeeper.

---