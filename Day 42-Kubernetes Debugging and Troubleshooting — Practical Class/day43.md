---

## Day 42: Kubernetes Debugging and Troubleshooting — Practical Class

### 📘 Overview

Debugging and troubleshooting are critical skills for managing Kubernetes clusters and workloads. This session focuses on identifying and resolving issues in pods, services, and other cluster resources using built-in Kubernetes tools and commands.

---


### Objectives

1. Understand the tools and commands for debugging in Kubernetes.
2. Diagnose and fix issues with pods, deployments, and services.
3. Use logs and events to identify root causes of failures.
4. Debug live pods using tools like `kubectl exec` and ephemeral containers.

---

### Key Concepts

1. **Kubernetes Events**:
   - Provide real-time information about what’s happening in the cluster.
   - Useful for diagnosing resource creation, updates, and errors.

2. **Pod Logs**:
   - Capture application output for debugging.
   - Can be viewed using `kubectl logs`.

3. **Ephemeral Containers**:
   - Temporary containers added to running pods for debugging.
   - Useful for troubleshooting without modifying the pod’s configuration.

4. **Common Issues**:
   - Image pull errors.
   - Pod scheduling issues.
   - Misconfigured services or ingress.

---