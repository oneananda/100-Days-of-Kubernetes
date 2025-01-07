---

## Day 62: Kubernetes Advanced Scheduling — Node Affinity and Anti-Affinity

### 📘 Overview

Node affinity and anti-affinity provide advanced scheduling capabilities that allow pods to be scheduled based on specific node attributes. These rules are more expressive and flexible than nodeSelector, enabling refined control over pod placement.

---

### Objectives

1. Understand node affinity and anti-affinity concepts.
2. Learn how to configure required and preferred affinity rules.
3. Explore use cases where affinity and anti-affinity can optimize pod placement.

---

### Key Concepts

1. **Node Affinity**:
   - Controls which nodes a pod can be scheduled on based on node labels.
   - **RequiredDuringSchedulingIgnoredDuringExecution**: Pod scheduling fails if the rules are not met.
   - **PreferredDuringSchedulingIgnoredDuringExecution**: Scheduler tries to honor rules but does not fail if they are unmet.

2. **Node Anti-Affinity**:
   - Ensures that pods avoid being scheduled on specific nodes based on labels.

3. **Use Cases**:
   - Distributing workloads across specific node groups.
   - Avoiding resource contention on nodes.
   - Enforcing compliance with hardware or location constraints.

---
