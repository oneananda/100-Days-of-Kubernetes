---

## Day 61: Kubernetes Advanced Scheduling — Introduction to Scheduling Concepts

### 📘 Overview

Kubernetes scheduling ensures that pods are placed on the best possible nodes based on resource requirements, constraints, and policies. This session introduces the foundational concepts of Kubernetes scheduling, including how the scheduler works, node selection criteria, and an overview of advanced scheduling techniques.

---

### Objectives

1. Understand the role of the Kubernetes scheduler in cluster management.
2. Learn about pod scheduling phases and decision-making criteria.
3. Explore basic scheduling features like nodeSelector and taints/tolerations.

---

### Key Concepts

1. **Kubernetes Scheduler**:
   - Automatically selects nodes for pods that don’t specify a node.
   - Factors like resource availability, constraints, and affinity rules influence its decisions.

2. **Scheduling Phases**:
   - **Pre-scheduling**: Ensures pods meet prerequisites like affinity rules.
   - **Scheduling**: Matches pods to nodes based on resources and policies.
   - **Post-scheduling**: Initiates pod creation and monitoring on the selected node.

3. **Basic Scheduling Features**:
   - **nodeSelector**: Assign pods to specific nodes based on labels.
   - **Taints and Tolerations**: Prevent or allow pods on certain nodes.
   - **Pod Affinity/Anti-Affinity**: Control placement based on proximity to other pods.

---

### Practical Exercises: Understanding the Basics

---

#### 1. Inspecting the Default Scheduler

##### Step 1: List Scheduler Components
Check the scheduler deployment:
```bash
kubectl get pods -n kube-system -l component=kube-scheduler
```

Describe the scheduler configuration:
```bash
kubectl describe pod -n kube-system <scheduler-pod-name>
```

##### Step 2: Monitor Scheduling Events
View events related to pod scheduling:
```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

---

#### 2. Using nodeSelector for Simple Scheduling

##### Step 1: Label a Node
Add a label to a node:
```bash
kubectl label nodes <node-name> environment=production
```

Verify the label:
```bash
kubectl get nodes --show-labels
```

##### Step 2: Assign Pods to the Labeled Node
Create a `node-selector-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: node-selector-pod
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    environment: production
```

Apply the configuration:
```bash
kubectl apply -f node-selector-pod.yaml
```

Verify the pod placement:
```bash
kubectl get pod node-selector-pod -o wide
```

---

#### 3. Exploring Taints and Tolerations

##### Step 1: Add a Taint to a Node
Taint a node to repel pods without tolerations:
```bash
kubectl taint nodes <node-name> key=value:NoSchedule
```

Verify the taint:
```bash
kubectl describe node <node-name>
```

##### Step 2: Create a Pod with Tolerations
Create a `toleration-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: toleration-pod
spec:
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f toleration-pod.yaml
```

Verify that the pod is scheduled on the tainted node:
```bash
kubectl get pod toleration-pod -o wide
```

---

#### Cleanup

Delete the created resources:
```bash
kubectl delete pod node-selector-pod toleration-pod
kubectl taint nodes <node-name> key-
kubectl label nodes <node-name> environment-
```

---
