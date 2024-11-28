---

## Day 22: Kubernetes DaemonSets — Running Pods on Every Node

### 📘 Overview

**Kubernetes DaemonSets** ensure that a copy of a pod is running on all (or some) nodes in a cluster. DaemonSets are commonly used for tasks like node monitoring, logging, or running system-level services. Today’s session focuses on understanding, creating, and managing DaemonSets in Kubernetes.

---

### What is a DaemonSet?

- **DaemonSet** is a Kubernetes resource that ensures a specific pod is scheduled on all or selected nodes.
- Use cases include:
  - **Node monitoring** (e.g., Prometheus Node Exporter).
  - **Log collection** (e.g., Fluentd, Filebeat).
  - **Cluster services** (e.g., DNS caching, storage management).

---
