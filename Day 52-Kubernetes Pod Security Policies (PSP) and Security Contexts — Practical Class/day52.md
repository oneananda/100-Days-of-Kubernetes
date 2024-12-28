---

## Day 52: Kubernetes Pod Security Policies (PSP) and Security Contexts — Practical Class

### 📘 Overview

Securing Kubernetes workloads is essential for protecting applications and data. **Pod Security Policies (PSPs)** and **Security Contexts** allow administrators to enforce security guidelines on pods and containers, such as restricting privileges, setting file system permissions, and controlling capabilities.

This session focuses on creating and applying Pod Security Policies and configuring Security Contexts for Kubernetes workloads.

---

### Objectives

1. Understand the purpose of Pod Security Policies and Security Contexts.
2. Create and apply Pod Security Policies to enforce security standards.
3. Configure Security Contexts to manage container privileges.
4. Test workload behavior under different security configurations.

---

### Key Concepts

1. **Pod Security Policies (PSPs)**:
   - Cluster-wide resources to define security constraints on pods.
   - Used to control privileges, host network access, volume usage, and more.

2. **Security Context**:
   - Per-container or per-pod configurations for security-related attributes like UID, GID, and filesystem access.

3. **Key Features**:
   - Restricting privileged access.
   - Forcing read-only file systems.
   - Limiting access to host namespaces.

4. **Deprecation**:
   - PSPs are deprecated in Kubernetes 1.21 and replaced by the Pod Security Admission (PSA) framework.

---
