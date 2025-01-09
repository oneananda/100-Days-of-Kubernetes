---

## Day 64: Kubernetes Advanced Scheduling — Taints, Tolerations, and Pod Topology Spread Constraints

### 📘 Overview

This session combines **taints and tolerations** with **pod topology spread constraints** to provide granular control over pod scheduling. These features are essential for managing resource isolation, high availability, and efficient workload distribution in Kubernetes clusters.

---

### Objectives

1. Understand how taints and tolerations influence pod scheduling.
2. Learn how pod topology spread constraints distribute workloads across the cluster.
3. Combine these techniques for advanced scheduling use cases.

---

### Key Concepts

1. **Taints and Tolerations**:
   - **Taints** prevent pods from being scheduled on certain nodes unless they tolerate the taint.
   - **Tolerations** allow pods to bypass taints and be scheduled on tainted nodes.

2. **Pod Topology Spread Constraints**:
   - Control how pods are distributed across nodes, zones, or regions.
   - Ensure even workload distribution or isolation across failure domains.

3. **Use Cases**:
   - Resource isolation for specific workloads.
   - High availability through pod distribution across failure zones.

---

### Practical Exercises: Taints, Tolerations, and Pod Topology Spread Constraints

---

#### 1. Configuring Taints and Tolerations

##### Step 1: Add a Taint to a Node
Add a taint to a node:
```bash
kubectl taint nodes <node-name> workload=high-priority:NoSchedule
```

Verify the taint:
```bash
kubectl describe node <node-name>
```

##### Step 2: Create a Pod with a Matching Toleration
Create a `toleration-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: toleration-pod
spec:
  tolerations:
  - key: "workload"
    operator: "Equal"
    value: "high-priority"
    effect: "NoSchedule"
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f toleration-pod.yaml
```

Verify the pod placement:
```bash
kubectl get pod toleration-pod -o wide
```

---

#### 2. Implementing Pod Topology Spread Constraints

##### Step 1: Create Pods with Topology Spread Constraints
Define a `topology-spread-pod.yaml` file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: spread-demo
spec:
  replicas: 6
  selector:
    matchLabels:
      app: spread-demo
  template:
    metadata:
      labels:
        app: spread-demo
    spec:
      topologySpreadConstraints:
      - maxSkew: 1
        topologyKey: "kubernetes.io/hostname"
        whenUnsatisfiable: DoNotSchedule
        labelSelector:
          matchLabels:
            app: spread-demo
      containers:
      - name: nginx
        image: nginx
```

Apply the configuration:
```bash
kubectl apply -f topology-spread-pod.yaml
```

##### Step 2: Verify Pod Distribution
Check the pod placement across nodes:
```bash
kubectl get pods -o wide
```

---

#### 3. Combining Taints, Tolerations, and Spread Constraints

##### Step 1: Add Taints to Nodes
Add taints to a group of nodes:
```bash
kubectl taint nodes <node-name-1> tier=frontend:NoSchedule
kubectl taint nodes <node-name-2> tier=frontend:NoSchedule
```

##### Step 2: Create Pods with Both Tolerations and Spread Constraints
Define a `combined-scheduling.yaml` file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: combined-scheduling-demo
spec:
  replicas: 4
  selector:
    matchLabels:
      app: combined-demo
  template:
    metadata:
      labels:
        app: combined-demo
    spec:
      tolerations:
      - key: "tier"
        operator: "Equal"
        value: "frontend"
        effect: "NoSchedule"
      topologySpreadConstraints:
      - maxSkew: 1
        topologyKey: "kubernetes.io/hostname"
        whenUnsatisfiable: DoNotSchedule
        labelSelector:
          matchLabels:
            app: combined-demo
      containers:
      - name: nginx
        image: nginx
```

Apply the configuration:
```bash
kubectl apply -f combined-scheduling.yaml
```

##### Step 3: Verify Combined Behavior
Check pod placement and ensure they are evenly distributed across tainted nodes:
```bash
kubectl get pods -o wide
```

---

#### Cleanup

Remove all created resources:
```bash
kubectl delete deployment spread-demo combined-scheduling-demo
kubectl delete pod toleration-pod
kubectl taint nodes <node-name-1> tier-
kubectl taint nodes <node-name-2> tier-
kubectl taint nodes <node-name> workload-
```

---

### Best Practices for Taints, Tolerations, and Topology Spread Constraints

1. **Minimize Over-Tainting**:
   - Use taints sparingly to avoid scheduling complexities.

2. **Combine Features**:
   - Leverage tolerations with topology spread constraints for advanced scenarios.

3. **Align with Failure Domains**:
   - Use topology keys like `zone` or `region` for high availability.

4. **Test in a Staging Environment**:
   - Verify configurations in a non-production environment before applying to critical workloads.

---
