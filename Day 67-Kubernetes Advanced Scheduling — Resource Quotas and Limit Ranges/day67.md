---

## Day 67: Kubernetes Advanced Scheduling — Resource Quotas and Limit Ranges

### 📘 Overview

**Resource Quotas** and **Limit Ranges** are Kubernetes features that ensure fair resource allocation among namespaces and prevent individual workloads from consuming excessive resources. This session explores how to manage resources efficiently using these tools.

---

### Objectives

1. Understand the role of resource quotas and limit ranges in Kubernetes.
2. Learn how to configure and enforce resource quotas for namespaces.
3. Use limit ranges to control resource requests and limits for individual pods and containers.

---

### Key Concepts

1. **Resource Quotas**:
   - Enforce resource usage limits (CPU, memory, storage, etc.) at the namespace level.
   - Prevent resource contention and ensure fair usage.

2. **Limit Ranges**:
   - Define default resource requests and limits for pods and containers within a namespace.
   - Prevent over-provisioning or under-provisioning of resources.

3. **Use Cases**:
   - Multi-tenant environments where namespaces represent teams or projects.
   - Enforcing resource usage policies for better cluster efficiency.

---
