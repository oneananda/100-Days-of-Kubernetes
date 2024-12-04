---

## Day 28: Kubernetes Node Affinity and Anti-Affinity — Optimizing Pod Placement

### 📘 Overview

**Node Affinity and Anti-Affinity** in Kubernetes allow you to control how pods are scheduled onto nodes based on specific node attributes. These mechanisms help optimize workload placement, resource utilization, and fault tolerance.

---


### What is Node Affinity and Anti-Affinity?

1. **Node Affinity**:
   - Ensures pods are scheduled on nodes that match specific criteria.
   - Similar to nodeSelector but more flexible, supporting expressions and operators.

2. **Node Anti-Affinity**:
   - Prevents pods from being scheduled on specific nodes.

3. **Affinity Types**:
   - **Required (Hard)**: Pods must be scheduled only on matching nodes.
   - **Preferred (Soft)**: Pods are ideally scheduled on matching nodes but can fall back to other nodes.

---

### Key Concepts

1. **Node Labels**:
   - Affinity rules rely on node labels for matching.
   - Example: `kubernetes.io/role=worker`

2. **Expressions and Operators**:
   - Operators like `In`, `NotIn`, `Exists`, `DoesNotExist` allow complex matching criteria.

3. **Use Cases**:
   - Scheduling workloads near specific resources (e.g., GPUs).
   - Isolating sensitive workloads on dedicated nodes.
   - Distributing workloads across fault domains.

---


### Hands-On with Node Affinity and Anti-Affinity

---

### 1. Labeling Nodes

#### Add Labels to Nodes:
```bash
kubectl label nodes <node-name> environment=production
kubectl label nodes <node-name> environment=staging
```

#### Verify Node Labels:
```bash
kubectl get nodes --show-labels
```

---

### 2. Configuring Node Affinity

#### Pod with Required Node Affinity:
Save the following as `node-affinity-hard.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-node-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: environment
            operator: In
            values:
            - production
  containers:
  - name: nginx
    image: nginx
```

#### Apply the Pod:
```bash
kubectl apply -f node-affinity-hard.yaml
```

#### Verify Pod Placement:
```bash
kubectl get pod pod-with-node-affinity -o wide
```

---

### 3. Configuring Node Anti-Affinity

#### Pod with Preferred Node Affinity:
Save the following as `node-affinity-soft.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-soft-affinity
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: environment
            operator: In
            values:
            - staging
  containers:
  - name: nginx
    image: nginx
```

#### Apply the Pod:
```bash
kubectl apply -f node-affinity-soft.yaml
```

#### Verify Pod Placement:
```bash
kubectl get pod pod-with-soft-affinity -o wide
```

---

### 4. Advanced Scenarios

#### Combining Affinity and Anti-Affinity:
Save the following as `combined-affinity.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-combined-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: environment
            operator: In
            values:
            - production
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchLabels:
            app: my-app
        topologyKey: "kubernetes.io/hostname"
  containers:
  - name: nginx
    image: nginx
```

#### Apply the Pod:
```bash
kubectl apply -f combined-affinity.yaml
```

#### Verify Pod Placement and Distribution:
```bash
kubectl describe pod pod-with-combined-affinity
```

---

### Best Practices

1. **Use Node Affinity for Resource Optimization**:
   - Schedule GPU-intensive workloads on GPU-enabled nodes.
   - Use labels to separate development, staging, and production environments.

2. **Avoid Overuse of Hard Affinity**:
   - Excessive constraints can lead to scheduling failures if no nodes meet the criteria.

3. **Combine Affinity and Anti-Affinity**:
   - Balance placement with fault tolerance by using both affinity and anti-affinity.

4. **Monitor Node Labels**:
   - Keep labels up to date to ensure proper pod placement.

---

### 📝 Document Your Progress

In your `day28.md` file, record:
- YAML configurations for affinity and anti-affinity.
- Observations on pod placement based on affinity rules.
- Insights on combining affinity and anti-affinity for workload distribution.

---

### 🎯 Outcome for Day 28

By the end of Day 28, you should:
1. Understand the role of Node Affinity and Anti-Affinity in Kubernetes scheduling.
2. Label nodes and configure pods with specific affinity rules.
3. Combine affinity and anti-affinity to optimize workload placement.
4. Apply best practices for effective resource utilization and fault tolerance.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Node Affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity)
- [Optimizing Workload Placement](https://kubernetes.io/docs/concepts/scheduling-eviction/)

---