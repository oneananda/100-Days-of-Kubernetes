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

### Hands-On with CRDs

In today’s session, we’ll create a CRD, define a custom resource, and interact with it using `kubectl`.

---

### 1. Creating a CRD

Define a CRD for a custom resource type `CronTab` that schedules jobs. Create a file named `crontab-crd.yaml`:

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: crontabs.example.com
spec:
  group: example.com
  names:
    plural: crontabs
    singular: crontab
    kind: CronTab
  scope: Namespaced
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                cronSpec:
                  type: string
                image:
                  type: string
                replicas:
                  type: integer
```

#### Apply the CRD:
```bash
kubectl apply -f crontab-crd.yaml
```

#### Verify the CRD:
```bash
kubectl get crd crontabs.example.com -o yaml
```

---


### 2. Creating a Custom Resource

Create a custom resource using the `CronTab` CRD. Save it in `example-crontab.yaml`:

```yaml
apiVersion: example.com/v1
kind: CronTab
metadata:
  name: my-crontab
spec:
  cronSpec: "*/5 * * * *"
  image: "nginx:latest"
  replicas: 3
```

#### Apply the Custom Resource:
```bash
kubectl apply -f example-crontab.yaml
```

#### Verify the Custom Resource:
```bash
kubectl get crontabs -o yaml
```

---


### 3. Automating with a Custom Controller (Optional)

Pair the CRD with a custom controller that watches `CronTab` resources and performs tasks such as creating Pods or Deployments. For now, we will explore the concept without implementation.

---

### 4. Testing and Validating CRDs

Use `kubectl` commands to interact with the custom resources:

- List all `CronTab` resources:
  ```bash
  kubectl get crontabs
  ```
- Describe a specific `CronTab`:
  ```bash
  kubectl describe crontab my-crontab
  ```

Test how schema validation handles incorrect specifications by applying a faulty resource definition.

---


### 📝 Document Your Progress

In your `day19.md` file, record:
- The YAML configuration for the CRD and custom resources.
- Steps to create and validate the CRD.
- Insights or challenges faced when working with CRDs.

---

### 🎯 Outcome for Day 19

By the end of Day 19, you should:
1. Understand how CRDs extend Kubernetes functionality.
2. Be able to create and manage CRDs and custom resources.
3. Know the basics of pairing CRDs with custom controllers for advanced automation.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Custom Resource Definitions](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/)
- [Building Controllers for CRDs](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#custom-controllers)

---