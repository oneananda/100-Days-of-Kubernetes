---

## Day 22: Kubernetes DaemonSets — Running Pods on Every Node

### 📘 Overview

**Kubernetes DaemonSets** ensure that a copy of a pod is running on all (or some) nodes in a cluster. DaemonSets are commonly used for tasks like node monitoring, logging, or running system-level services. Today’s session focuses on understanding, creating, and managing DaemonSets in Kubernetes.

---

### What is a DaemonSet?

- **DaemonSet** is a Kubernetes resource that ensures a specific pod is scheduled on all or selected nodes.
- Use cases include:
  - **Node monitoring** (e.g., Prometheus Node Exporter).
  - **Log collection** (e.g., Fluentd, Filebeat).
  - **Cluster services** (e.g., DNS caching, storage management).

---


### Key Concepts

1. **Node Affinity and Tolerations**:
   - You can control which nodes DaemonSet pods are scheduled on using node selectors, affinities, or tolerations.
   
2. **Rolling Updates**:
   - DaemonSets support rolling updates, enabling non-disruptive updates to the pods.

3. **Pod Management**:
   - Like Deployments, DaemonSets ensure pods remain in the desired state.

---


### Hands-On with DaemonSets

In today’s session, we’ll create and manage DaemonSets and explore their practical use cases.

---

### 1. Creating a Basic DaemonSet

Deploy a simple DaemonSet to run `nginx` on every node. Save the following YAML as `nginx-daemonset.yaml`:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemonset
  labels:
    app: nginx
spec:
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
        image: nginx:1.21
        ports:
        - containerPort: 80
```

#### Apply the DaemonSet:
```bash
kubectl apply -f nginx-daemonset.yaml
```

#### Verify the DaemonSet:
```bash
kubectl get daemonsets
kubectl get pods -o wide
```

---


### 2. Using Node Selectors

Run the DaemonSet on specific nodes by adding a node selector. Update `nginx-daemonset.yaml`:

```yaml
spec:
  template:
    spec:
      nodeSelector:
        kubernetes.io/os: linux
```

#### Apply the Changes:
```bash
kubectl apply -f nginx-daemonset.yaml
```

#### Verify the Pods Placement:
```bash
kubectl get pods -o wide
```

---
