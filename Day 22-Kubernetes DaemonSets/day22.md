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


### 3. Adding Tolerations for Tainted Nodes

Deploy the DaemonSet on nodes with specific taints. Update `nginx-daemonset.yaml`:

```yaml
spec:
  template:
    spec:
      tolerations:
      - key: "example-key"
        operator: "Equal"
        value: "example-value"
        effect: "NoSchedule"
```

#### Apply the Changes:
```bash
kubectl apply -f nginx-daemonset.yaml
```

#### Verify the Pods Placement:
```bash
kubectl describe pods
```

---


### 4. Performing Rolling Updates

Update the container image for the DaemonSet:

```yaml
containers:
- name: nginx
  image: nginx:1.23
```

#### Apply the Updated YAML:
```bash
kubectl apply -f nginx-daemonset.yaml
```

#### Monitor the Update:
```bash
kubectl rollout status daemonset nginx-daemonset
```

---

### 5. Practical Use Case: Node Monitoring with DaemonSet

Deploy a **Node Exporter** DaemonSet for monitoring nodes:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      containers:
      - name: node-exporter
        image: quay.io/prometheus/node-exporter:v1.6.0
        ports:
        - containerPort: 9100
```

#### Apply the DaemonSet:
```bash
kubectl apply -f node-exporter.yaml
```

#### Verify the Deployment:
```bash
kubectl get daemonsets
kubectl get pods -o wide
```

---