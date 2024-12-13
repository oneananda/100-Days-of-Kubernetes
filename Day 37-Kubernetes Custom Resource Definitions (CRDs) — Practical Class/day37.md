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


### Practical Exercises

---

### 1. Creating a Custom Resource Definition

#### Define a CRD:
Save the following YAML as `crd.yaml`:

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: widgets.example.com
spec:
  group: example.com
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
              size:
                type: string
                enum:
                - small
                - medium
                - large
  scope: Namespaced
  names:
    plural: widgets
    singular: widget
    kind: Widget
    shortNames:
    - wd
```

#### Apply the CRD:
```bash
kubectl apply -f crd.yaml
```

#### Verify the CRD:
```bash
kubectl get crd
kubectl describe crd widgets.example.com
```

---

### 2. Creating a Custom Resource (CR)

#### Define a CR:
Save the following YAML as `widget.yaml`:

```yaml
apiVersion: example.com/v1
kind: Widget
metadata:
  name: my-widget
  namespace: default
spec:
  size: medium
```

#### Apply the Custom Resource:
```bash
kubectl apply -f widget.yaml
```

#### Verify the Custom Resource:
```bash
kubectl get widget my-widget
kubectl describe widget my-widget
```

---

### 3. Managing Custom Resources with a Controller

#### Install a Sample Controller:
Use the Kubernetes sample controller to handle custom resources. Clone the repository:

```bash
git clone https://github.com/kubernetes/sample-controller.git
cd sample-controller
```

#### Deploy the Controller:
1. Build and push the sample controller to your container registry.
2. Deploy the controller as a Kubernetes Deployment (update the image URL in the manifest):
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: widget-controller
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: widget-controller
     template:
       metadata:
         labels:
           app: widget-controller
       spec:
         containers:
         - name: controller
           image: <your-registry>/widget-controller:latest
   ```
3. Apply the Deployment:
   ```bash
   kubectl apply -f deployment.yaml
   ```

---

### 4. Testing the Controller

#### Modify the Custom Resource:
Update `widget.yaml` to change the `size` value:
```yaml
spec:
  size: large
```

Apply the updated resource:
```bash
kubectl apply -f widget.yaml
```

#### Observe the Controller's Actions:
Check the logs of the controller:
```bash
kubectl logs -l app=widget-controller
```

Verify the resource state is reconciled as expected.

---

### 5. Cleanup

Remove the CRD and associated resources:
```bash
kubectl delete crd widgets.example.com
kubectl delete deployment widget-controller
```

---