---

## Day 66: Kubernetes Advanced Scheduling — Custom Schedulers and Extensibility

### 📘 Overview

Kubernetes provides a default scheduler, but some workloads may require customized scheduling logic. Kubernetes allows the use of **custom schedulers** and extensions to tailor pod placement based on specific business or technical requirements. This session focuses on creating and deploying custom schedulers and leveraging the Kubernetes Scheduling Framework for extensibility.

---

### Objectives

1. Understand the role of custom schedulers in Kubernetes.
2. Learn how to configure and use a custom scheduler.
3. Explore the Kubernetes Scheduling Framework for advanced extensibility.

---

### Key Concepts

1. **Custom Schedulers**:
   - Separate from the default Kubernetes scheduler.
   - Used to implement unique scheduling logic for specific workloads.

2. **Kubernetes Scheduling Framework**:
   - Provides hooks and plugins for extending the scheduling process.
   - Supports features like custom scoring, filtering, and preemption logic.

3. **Use Cases**:
   - Workloads with strict placement constraints or dependencies.
   - Custom scoring algorithms for resource optimization.

---
