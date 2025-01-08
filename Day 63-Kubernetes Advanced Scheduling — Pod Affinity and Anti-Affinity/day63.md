---

## Day 63: Kubernetes Advanced Scheduling — Pod Affinity and Anti-Affinity

### 📘 Overview

While **node affinity** controls where pods are scheduled based on node labels, **pod affinity** and **anti-affinity** manage pod placement based on the presence or absence of other pods. These features are useful for ensuring colocation of related pods or avoiding resource contention.

---

### Objectives

1. Understand the difference between pod affinity and anti-affinity.
2. Learn to configure required and preferred affinity rules.
3. Explore use cases where pod affinity and anti-affinity improve application performance or reliability.

---

### Key Concepts

1. **Pod Affinity**:
   - Ensures that pods are scheduled on the same node or nearby nodes as other specified pods.
   - Use cases: Colocation of microservices for low-latency communication.

2. **Pod Anti-Affinity**:
   - Ensures that pods are scheduled away from specific pods to avoid contention.
   - Use cases: Spreading replicas across nodes for high availability.

3. **Required and Preferred Rules**:
   - **RequiredDuringSchedulingIgnoredDuringExecution**: Mandatory rules for scheduling.
   - **PreferredDuringSchedulingIgnoredDuringExecution**: Best-effort rules for placement.

---

### Practical Exercises: Pod Affinity and Anti-Affinity

---

#### 1. Configuring Pod Affinity

##### Step 1: Deploy a Base Pod
Create a base pod to which other pods will apply affinity rules:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: base-pod
  labels:
    app: base
spec:
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f base-pod.yaml
```

##### Step 2: Create a Pod with Pod Affinity
Create a `pod-affinity-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-affinity-pod
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchLabels:
            app: base
        topologyKey: "kubernetes.io/hostname"
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f pod-affinity-pod.yaml
```

Verify that the pod is scheduled on the same node as `base-pod`:
```bash
kubectl get pod -o wide
```

---

#### 2. Configuring Pod Anti-Affinity

##### Step 1: Create a Pod with Anti-Affinity
Create a `pod-anti-affinity-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-anti-affinity-pod
spec:
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchLabels:
            app: base
        topologyKey: "kubernetes.io/hostname"
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f pod-anti-affinity-pod.yaml
```

Verify that the pod is scheduled on a different node than `base-pod`:
```bash
kubectl get pod -o wide
```

---

#### 3. Using Preferred Rules

##### Step 1: Create a Pod with Preferred Affinity Rules
Create a `preferred-pod-affinity-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: preferred-pod-affinity-pod
spec:
  affinity:
    podAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        podAffinityTerm:
          labelSelector:
            matchLabels:
              app: base
          topologyKey: "kubernetes.io/hostname"
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f preferred-pod-affinity-pod.yaml
```

Check the pod placement and note if the preference was followed:
```bash
kubectl get pod -o wide
```

---

#### Cleanup

Remove all created resources:
```bash
kubectl delete pod base-pod pod-affinity-pod pod-anti-affinity-pod preferred-pod-affinity-pod
```

---

### Best Practices for Pod Affinity and Anti-Affinity

1. **Balance Rules**:
   - Use affinity for improved communication and anti-affinity for reliability.

2. **Understand Topology**:
   - Use appropriate topology keys, such as `kubernetes.io/hostname` or `failure-domain.beta.kubernetes.io/zone`.

3. **Test Rules**:
   - Ensure that affinity and anti-affinity rules achieve desired behavior without over-constraining scheduling.

4. **Combine Affinity Types**:
   - Mix node affinity and pod affinity to optimize both resource utilization and application placement.
