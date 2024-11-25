---

## Day 19: Custom Resource Definitions (CRDs) — Extending Kubernetes

### 📘 Overview

**Custom Resource Definitions (CRDs)** enable you to extend Kubernetes by defining custom resource types that fit your application's needs. They are a powerful feature for building extensible systems while leveraging Kubernetes' declarative model and APIs. Today’s session focuses on understanding and working with CRDs.

---

### What are CRDs?

- **CRDs** allow users to create their own Kubernetes resources, beyond the built-in ones like Pods, Services, or Deployments.
- They act as a blueprint for custom objects, enabling Kubernetes to understand and manage them.
- CRDs are commonly used alongside custom controllers to implement advanced automation.

---

### Key Concepts

1. **Custom Resources**:
   - User-defined Kubernetes objects based on CRDs.
   - Defined using YAML/JSON manifests, like built-in resources.

2. **CustomResourceDefinition**:
   - A special API object that registers the new resource type with the Kubernetes API server.

3. **Custom Controllers**:
   - Optional, but they enhance CRDs by adding custom logic to manage the lifecycle of custom resources.

4. **Versioning and Schema Validation**:
   - CRDs support multiple versions for resource compatibility.
   - Schema validation ensures the custom resources follow the expected structure.

---