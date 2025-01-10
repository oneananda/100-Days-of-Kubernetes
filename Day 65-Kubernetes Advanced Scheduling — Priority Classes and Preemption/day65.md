---

## Day 65: Kubernetes Advanced Scheduling — Priority Classes and Preemption

### 📘 Overview

**Priority Classes** and **Preemption** are critical features in Kubernetes that enable efficient scheduling during resource contention. Priority classes determine the importance of workloads, while preemption allows high-priority pods to replace lower-priority pods when resources are constrained.

---

### Objectives

1. Understand the concept of priority classes and their role in scheduling.
2. Learn how preemption works in Kubernetes.
3. Configure and test priority classes to handle resource contention scenarios.

---

### Key Concepts

1. **Priority Classes**:
   - Assign relative importance to workloads.
   - Pods with higher priority are scheduled first when resources are limited.

2. **Preemption**:
   - Enables high-priority pods to evict lower-priority pods to free up resources.
   - Helps ensure critical workloads are not blocked by less important workloads.

3. **Default Priority Classes**:
   - `system-cluster-critical`: For cluster-level critical components.
   - `system-node-critical`: For node-level critical components.

4. **Custom Priority Classes**:
   - Users can create priority classes for specific workloads.

---
