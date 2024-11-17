---

## Day 11: Working with Namespaces — Organizing and Isolating Cluster Resources

### 📘 Overview

**Namespaces** in Kubernetes provide a way to divide cluster resources between multiple users or projects. They are useful for isolating environments (e.g., development, staging, production) and organizing resources for better management.

---

### What is a Namespace?

- A Namespace is a virtual cluster within a Kubernetes cluster.
- Namespaces allow multiple teams or applications to share a cluster without interfering with each other.
- Resources like Pods, Services, and Deployments can be assigned to specific Namespaces, and their scope is limited to that Namespace.

---

### Key Features of Namespaces

1. **Resource Isolation**: Separate resources for different environments or teams.
2. **Resource Quotas**: Limit the resources (CPU, memory, etc.) used by a Namespace.
3. **Access Control**: Restrict access to resources within a Namespace using RBAC (Role-Based Access Control).
4. **Default Namespace**: If no Namespace is specified, resources are created in the `default` Namespace.

---
