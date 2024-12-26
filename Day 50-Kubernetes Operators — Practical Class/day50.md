---

## Day 50: Kubernetes Operators — Practical Class

### 📘 Overview

**Kubernetes Operators** extend the capabilities of Kubernetes by managing complex applications and automating their lifecycle tasks. Operators are built using Custom Resource Definitions (CRDs) and Controllers, making it easier to manage stateful and custom applications in Kubernetes.

This session focuses on creating and deploying a simple Operator, understanding its architecture, and managing resources using CRDs.

---

### Objectives

1. Understand the purpose and architecture of Kubernetes Operators.
2. Create a Custom Resource Definition (CRD) for a custom application.
3. Deploy and test a simple Operator.
4. Manage the application lifecycle using the Operator.

---

### Key Concepts

1. **Operators**:
   - Software extensions that automate the management of complex applications on Kubernetes.
   - Implement domain-specific knowledge via Controllers.

2. **Custom Resource Definitions (CRDs)**:
   - Define custom resources in Kubernetes, extending its API to manage new types of resources.

3. **Controller**:
   - Watches the custom resources and takes action to reconcile the desired state.

4. **Operator Lifecycle**:
   - Automates tasks such as deployment, scaling, upgrades, and backups for applications.

---

### Practical Exercises

---

### 1. Creating a Custom Resource Definition (CRD)

#### Step 1: Define the CRD
Create a `crd-example.yaml` file:
```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: myapps.example.com
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
              replicas:
                type: integer
              image:
                type: string
  scope: Namespaced
  names:
    plural: myapps
    singular: myapp
    kind: MyApp
    shortNames:
    - ma
```

Apply the CRD:
```bash
kubectl apply -f crd-example.yaml
```

Verify the CRD:
```bash
kubectl get crd
```

---

### 2. Deploying a Custom Resource

#### Step 1: Create a Custom Resource (CR)
Create a `myapp-instance.yaml` file:
```yaml
apiVersion: example.com/v1
kind: MyApp
metadata:
  name: myapp-instance
spec:
  replicas: 2
  image: nginx:1.19
```

Apply the Custom Resource:
```bash
kubectl apply -f myapp-instance.yaml
```

Verify the Custom Resource:
```bash
kubectl get myapps
kubectl describe myapp myapp-instance
```

---

### 3. Deploying a Simple Operator

#### Step 1: Deploy a Controller for the CR
Create a `myapp-operator.yaml` file with a simple operator logic:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-operator
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp-operator
  template:
    metadata:
      labels:
        app: myapp-operator
    spec:
      containers:
      - name: operator
        image: busybox
        command:
          - sh
          - -c
          - |
            while true; do
              echo "Reconciling MyApp instances";
              # Simulate reconciling logic here (e.g., scaling or updating pods)
              sleep 10;
            done
```

Apply the Operator Deployment:
```bash
kubectl apply -f myapp-operator.yaml
```

Verify the Operator:
```bash
kubectl get pods -l app=myapp-operator
```

---

### 4. Testing the Operator

#### Step 1: Update the Custom Resource
Modify `myapp-instance.yaml` to change the number of replicas:
```yaml
spec:
  replicas: 3
```

Apply the updated Custom Resource:
```bash
kubectl apply -f myapp-instance.yaml
```

Monitor the Operator logs for actions taken:
```bash
kubectl logs -l app=myapp-operator
```

---

### 5. Cleanup

Remove all resources:
```bash
kubectl delete -f myapp-instance.yaml
kubectl delete -f myapp-operator.yaml
kubectl delete -f crd-example.yaml
```

---
