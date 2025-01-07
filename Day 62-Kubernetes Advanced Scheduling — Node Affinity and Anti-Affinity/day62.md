---

## Day 62: Kubernetes Advanced Scheduling — Node Affinity and Anti-Affinity

### 📘 Overview

Node affinity and anti-affinity provide advanced scheduling capabilities that allow pods to be scheduled based on specific node attributes. These rules are more expressive and flexible than nodeSelector, enabling refined control over pod placement.

---

### Objectives

1. Understand node affinity and anti-affinity concepts.
2. Learn how to configure required and preferred affinity rules.
3. Explore use cases where affinity and anti-affinity can optimize pod placement.

---

### Key Concepts

1. **Node Affinity**:
   - Controls which nodes a pod can be scheduled on based on node labels.
   - **RequiredDuringSchedulingIgnoredDuringExecution**: Pod scheduling fails if the rules are not met.
   - **PreferredDuringSchedulingIgnoredDuringExecution**: Scheduler tries to honor rules but does not fail if they are unmet.

2. **Node Anti-Affinity**:
   - Ensures that pods avoid being scheduled on specific nodes based on labels.

3. **Use Cases**:
   - Distributing workloads across specific node groups.
   - Avoiding resource contention on nodes.
   - Enforcing compliance with hardware or location constraints.

---

### Practical Exercises: Node Affinity and Anti-Affinity

---

#### 1. Configuring Node Affinity

##### Step 1: Label Nodes
Add labels to nodes:
```bash
kubectl label nodes <node-name-1> zone=east
kubectl label nodes <node-name-2> zone=west
```

Verify the labels:
```bash
kubectl get nodes --show-labels
```

##### Step 2: Create a Pod with Node Affinity
Create a `node-affinity-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: node-affinity-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: zone
            operator: In
            values:
            - east
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f node-affinity-pod.yaml
```

Verify that the pod is scheduled on the correct node:
```bash
kubectl get pod node-affinity-pod -o wide
```

---

#### 2. Configuring Preferred Node Affinity

##### Step 1: Create a Pod with Preferred Node Affinity
Create a `preferred-node-affinity-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: preferred-node-affinity-pod
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: zone
            operator: In
            values:
            - west
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f preferred-node-affinity-pod.yaml
```

Check the pod placement and verify whether the preferred rule was followed:
```bash
kubectl get pod preferred-node-affinity-pod -o wide
```

---

#### 3. Configuring Node Anti-Affinity

##### Step 1: Create a Pod with Node Anti-Affinity
Create a `node-anti-affinity-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: node-anti-affinity-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: zone
            operator: NotIn
            values:
            - west
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f node-anti-affinity-pod.yaml
```

Verify that the pod avoids nodes labeled with `zone=west`:
```bash
kubectl get pod node-anti-affinity-pod -o wide
```

---

#### Cleanup

Remove the created resources:
```bash
kubectl delete pod node-affinity-pod preferred-node-affinity-pod node-anti-affinity-pod
kubectl label nodes <node-name-1> zone-
kubectl label nodes <node-name-2> zone-
```

---
