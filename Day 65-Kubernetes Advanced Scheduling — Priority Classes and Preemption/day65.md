---

## Day 65: Kubernetes Advanced Scheduling — Priority Classes and Preemption

### 📘 Overview

**Priority Classes** and **Preemption** are critical features in Kubernetes that enable efficient scheduling during resource contention. Priority classes determine the importance of workloads, while preemption allows high-priority pods to replace lower-priority pods when resources are constrained.

---

### Objectives

1. Understand the concept of priority classes and their role in scheduling.
2. Learn how preemption works in Kubernetes.
3. Configure and test priority classes to handle resource contention scenarios.

---

### Key Concepts

1. **Priority Classes**:
   - Assign relative importance to workloads.
   - Pods with higher priority are scheduled first when resources are limited.

2. **Preemption**:
   - Enables high-priority pods to evict lower-priority pods to free up resources.
   - Helps ensure critical workloads are not blocked by less important workloads.

3. **Default Priority Classes**:
   - `system-cluster-critical`: For cluster-level critical components.
   - `system-node-critical`: For node-level critical components.

4. **Custom Priority Classes**:
   - Users can create priority classes for specific workloads.

---

### Practical Exercises: Configuring Priority Classes and Preemption

---

#### 1. Creating Custom Priority Classes

##### Step 1: Define Priority Classes
Create a `priority-classes.yaml` file:
```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000
globalDefault: false
description: "High-priority class for critical workloads."
---
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 500
globalDefault: false
description: "Low-priority class for non-critical workloads."
```

Apply the configuration:
```bash
kubectl apply -f priority-classes.yaml
```

List all priority classes:
```bash
kubectl get priorityclasses
```

---

#### 2. Testing Priority Scheduling

##### Step 1: Deploy a Low-Priority Pod
Create a `low-priority-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: low-priority-pod
spec:
  priorityClassName: low-priority
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f low-priority-pod.yaml
```

##### Step 2: Deploy a High-Priority Pod
Create a `high-priority-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: high-priority-pod
spec:
  priorityClassName: high-priority
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f high-priority-pod.yaml
```

Verify the pod placements:
```bash
kubectl get pods -o wide
```

---

#### 3. Testing Preemption

##### Step 1: Simulate Resource Contention
Create a resource-intensive pod to consume cluster resources:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: resource-intensive
spec:
  replicas: 2
  selector:
    matchLabels:
      app: intensive
  template:
    metadata:
      labels:
        app: intensive
    spec:
      containers:
      - name: stress
        image: polinux/stress
        args:
        - "--cpu"
        - "4"
```

Apply the configuration:
```bash
kubectl apply -f resource-intensive.yaml
```

##### Step 2: Deploy a High-Priority Pod
Apply the `high-priority-pod.yaml` file again.

Verify preemption by checking the status of low-priority pods:
```bash
kubectl get pods
kubectl describe pod low-priority-pod
```

Observe that low-priority pods are evicted to free up resources for the high-priority pod.

---

#### Cleanup

Remove all created resources:
```bash
kubectl delete -f priority-classes.yaml
kubectl delete -f low-priority-pod.yaml
kubectl delete -f high-priority-pod.yaml
kubectl delete deployment resource-intensive
```

---
