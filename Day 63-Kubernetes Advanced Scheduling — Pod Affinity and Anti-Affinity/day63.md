---

## Day 63: Kubernetes Advanced Scheduling — Pod Affinity and Anti-Affinity

### 📘 Overview

While **node affinity** controls where pods are scheduled based on node labels, **pod affinity** and **anti-affinity** manage pod placement based on the presence or absence of other pods. These features are useful for ensuring colocation of related pods or avoiding resource contention.

---

### Objectives

1. Understand the difference between pod affinity and anti-affinity.
2. Learn to configure required and preferred affinity rules.
3. Explore use cases where pod affinity and anti-affinity improve application performance or reliability.

---

### Key Concepts

1. **Pod Affinity**:
   - Ensures that pods are scheduled on the same node or nearby nodes as other specified pods.
   - Use cases: Colocation of microservices for low-latency communication.

2. **Pod Anti-Affinity**:
   - Ensures that pods are scheduled away from specific pods to avoid contention.
   - Use cases: Spreading replicas across nodes for high availability.

3. **Required and Preferred Rules**:
   - **RequiredDuringSchedulingIgnoredDuringExecution**: Mandatory rules for scheduling.
   - **PreferredDuringSchedulingIgnoredDuringExecution**: Best-effort rules for placement.

---
