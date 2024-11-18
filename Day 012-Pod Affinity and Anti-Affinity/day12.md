---

## Day 12: Pod Affinity and Anti-Affinity — Controlling Pod Placement

### 📘 Overview

**Pod Affinity and Anti-Affinity** are advanced scheduling techniques in Kubernetes that let you control how Pods are co-located or spread across nodes. This is especially useful for optimizing performance, improving fault tolerance, and managing workloads efficiently.

---

### What is Pod Affinity and Anti-Affinity?

- **Pod Affinity** ensures that Pods are scheduled close to each other, improving locality for data sharing or inter-application communication.
- **Pod Anti-Affinity** ensures that Pods are scheduled away from each other, which helps spread workloads and improve fault tolerance.

---

### Key Features

1. **Topology Awareness**: Pods can be scheduled based on node labels, such as `kubernetes.io/hostname`.
2. **Hard and Soft Rules**:
   - **RequiredDuringSchedulingIgnoredDuringExecution**: Rules that must be satisfied for scheduling.
   - **PreferredDuringSchedulingIgnoredDuringExecution**: Rules that the scheduler tries to satisfy but are not mandatory.
3. **Use Cases**:
   - Pod Affinity: Databases with primary and replica Pods for better performance.
   - Pod Anti-Affinity: Distributed systems requiring Pods to run on different nodes for reliability.

---

### Hands-On with Pod Affinity and Anti-Affinity

Let’s explore how to implement Pod Affinity and Anti-Affinity.

---

### 1. Pod Affinity Example

#### Use Case: Scheduling a Pod on the same node as another Pod labeled `app: frontend`.

Create a YAML file named `pod-affinity.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-affinity
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchLabels:
              app: frontend
          topologyKey: "kubernetes.io/hostname"
  containers:
    - name: nginx
      image: nginx
```

#### Apply the configuration:
```bash
kubectl apply -f pod-affinity.yaml
```

#### Verify Pod Placement:
```bash
kubectl get pods -o wide
```

---


### 2. Pod Anti-Affinity Example

#### Use Case: Ensuring Pods labeled `app: backend` are not scheduled on the same node.

Create a YAML file named `pod-anti-affinity.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-anti-affinity
spec:
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchLabels:
              app: backend
          topologyKey: "kubernetes.io/hostname"
  containers:
    - name: nginx
      image: nginx
```

#### Apply the configuration:
```bash
kubectl apply -f pod-anti-affinity.yaml
```

#### Verify Pod Placement:
```bash
kubectl get pods -o wide
```

---


### 3. Combining Affinity and Anti-Affinity

You can use both Affinity and Anti-Affinity to fine-tune Pod placement. For example:

```yaml
affinity:
  podAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        podAffinityTerm:
          labelSelector:
            matchLabels:
              app: frontend
          topologyKey: "kubernetes.io/hostname"
  podAntiAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchLabels:
            app: backend
        topologyKey: "kubernetes.io/hostname"
```

---


### 📝 Document Your Progress

In your `day12.md` file, record:
- Configurations for Pod Affinity and Anti-Affinity.
- Commands used for applying and verifying configurations.
- Observations on how Pods are placed and any challenges faced.

---

### 🎯 Outcome for Day 12

By the end of Day 12, you should:
1. Understand how to use Pod Affinity and Anti-Affinity for advanced scheduling.
2. Be able to write YAML configurations to control Pod placement.
3. Appreciate how these features improve performance and fault tolerance.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Pod Affinity and Anti-Affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity)
- [Topology Aware Scheduling](https://kubernetes.io/docs/concepts/scheduling-eviction/topology-aware-scheduling/)

--- 