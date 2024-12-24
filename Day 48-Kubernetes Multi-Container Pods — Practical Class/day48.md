---

## Day 48: Kubernetes Multi-Container Pods — Practical Class

### 📘 Overview

Kubernetes pods can run multiple containers that share resources such as storage and network. **Multi-Container Pods** enable containers to work closely together within the same pod, supporting use cases like sidecar patterns, logging agents, and helper containers.

This session focuses on creating multi-container pods, understanding container communication within a pod, and implementing common patterns.

---

### Objectives

1. Understand the use cases for multi-container pods.
2. Create and configure multi-container pods.
3. Enable communication between containers within a pod.
4. Implement sidecar and adapter patterns.

---

### Key Concepts

1. **Multi-Container Pod**:
   - A pod that runs two or more containers sharing the same storage, network namespace, and IP address.

2. **Container Communication**:
   - Containers in the same pod communicate via `localhost`.

3. **Common Patterns**:
   - **Sidecar Pattern**: Augments the functionality of the main container.
   - **Adapter Pattern**: Acts as a translator between the main container and external systems.
   - **Ambassador Pattern**: Manages connections to external systems.

---
