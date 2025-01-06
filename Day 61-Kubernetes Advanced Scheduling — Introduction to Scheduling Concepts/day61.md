---

## Day 61: Kubernetes Advanced Scheduling — Introduction to Scheduling Concepts

### 📘 Overview

Kubernetes scheduling ensures that pods are placed on the best possible nodes based on resource requirements, constraints, and policies. This session introduces the foundational concepts of Kubernetes scheduling, including how the scheduler works, node selection criteria, and an overview of advanced scheduling techniques.

---

### Objectives

1. Understand the role of the Kubernetes scheduler in cluster management.
2. Learn about pod scheduling phases and decision-making criteria.
3. Explore basic scheduling features like nodeSelector and taints/tolerations.

---

### Key Concepts

1. **Kubernetes Scheduler**:
   - Automatically selects nodes for pods that don’t specify a node.
   - Factors like resource availability, constraints, and affinity rules influence its decisions.

2. **Scheduling Phases**:
   - **Pre-scheduling**: Ensures pods meet prerequisites like affinity rules.
   - **Scheduling**: Matches pods to nodes based on resources and policies.
   - **Post-scheduling**: Initiates pod creation and monitoring on the selected node.

3. **Basic Scheduling Features**:
   - **nodeSelector**: Assign pods to specific nodes based on labels.
   - **Taints and Tolerations**: Prevent or allow pods on certain nodes.
   - **Pod Affinity/Anti-Affinity**: Control placement based on proximity to other pods.

---
