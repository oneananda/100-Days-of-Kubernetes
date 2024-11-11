---

## Day 5: Working with ReplicaSets — Ensuring High Availability

### 📘 Overview

A **ReplicaSet** is a Kubernetes object that ensures a specified number of identical Pods are always running in a cluster. It's a key concept for maintaining **high availability** and **fault tolerance**. Understanding ReplicaSets is essential for deploying resilient applications.

---

### What is a ReplicaSet?

- A ReplicaSet helps maintain a **stable set of pod replicas** running at any time.
- It automatically replaces any Pods that fail or are terminated to maintain the desired count.
- ReplicaSets allow applications to be **highly available** and **scalable** by managing multiple instances of Pods.

---

### Key Characteristics of ReplicaSets

1. **Desired Replicas**: The number of Pods a ReplicaSet should manage.
2. **Selector**: Matches labels on Pods to identify which Pods the ReplicaSet is responsible for.
3. **Pod Template**: Specifies the configuration for Pods (e.g., container image, resources) the ReplicaSet manages.
4. **Automatic Scaling**: Although manual by default, ReplicaSets can be scaled by changing the `replicas` value.

---

### Hands-On with ReplicaSets

In this section, we’ll explore creating, managing, and scaling ReplicaSets using `kubectl` commands.

---

### 1. Creating ReplicaSets

You can define a ReplicaSet in a YAML manifest file, specifying the desired replica count, Pod template, and selectors.

#### Example: ReplicaSet YAML Configuration

This example configures a ReplicaSet to maintain **three replicas** of an Nginx Pod:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

Save this file as `nginx-replicaset.yaml` and create the ReplicaSet with:

```bash
kubectl apply -f nginx-replicaset.yaml
```

- This command creates a ReplicaSet named `nginx-replicaset` that will maintain three replicas of an Nginx container.

---

### 2. Inspecting ReplicaSets

Once created, you can view the ReplicaSet’s status and its managed Pods with various `kubectl` commands.

#### View ReplicaSets
```bash
kubectl get rs
```

This command lists all ReplicaSets, showing their desired, current, and ready replica counts.

#### Describe a ReplicaSet
```bash
kubectl describe rs nginx-replicaset
```

This command provides details about the ReplicaSet, including:
- Events related to the creation and management of Pods.
- Status of each replica and error messages, if any.

#### View Pods Created by the ReplicaSet
```bash
kubectl get pods -l app=nginx
```

This lists all Pods with the `app=nginx` label, which are managed by the ReplicaSet.

---
