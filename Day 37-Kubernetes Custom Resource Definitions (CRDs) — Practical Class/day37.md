---

## Day 37: Kubernetes Custom Resource Definitions (CRDs) — Practical Class

### 📘 Overview

**Custom Resource Definitions (CRDs)** enable users to extend Kubernetes by defining their own resource types. These resources can be managed like built-in Kubernetes objects, allowing customization for specific application needs.

This practical session focuses on creating, managing, and using CRDs with controllers for custom workflows.

---


### Objectives

1. Understand the purpose of CRDs in Kubernetes.
2. Create and apply a CRD.
3. Manage custom resources using `kubectl`.
4. Use a controller to handle custom resource reconciliation.

---

### Key Concepts

1. **Custom Resource Definition (CRD)**:
   - Defines the schema and behavior of a custom Kubernetes resource.

2. **Custom Resource (CR)**:
   - Instances of a CRD managed like standard Kubernetes resources.

3. **Controller**:
   - Watches and reconciles the desired state of custom resources with the cluster state.

4. **APIs and Extensions**:
   - CRDs allow you to extend the Kubernetes API for your application.

---