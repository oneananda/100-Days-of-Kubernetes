﻿---

## Day 68: Kubernetes Advanced Scheduling — Combining Techniques for Optimized Workloads

### 📘 Overview

To fully leverage Kubernetes' scheduling capabilities, it’s essential to combine multiple advanced scheduling techniques. This session focuses on integrating Node Affinity, Pod Affinity/Anti-Affinity, Taints and Tolerations, Priority Classes, Preemption, and Topology Spread Constraints to build efficient, resilient, and optimized workloads.


---

### Objectives

1. Learn how to combine multiple scheduling strategies for better workload placement.
2. Optimize resource utilization while ensuring high availability and performance.
3. Apply best practices for real-world scenarios in multi-tenant clusters.

---

### Key Concepts

1. Multi-Constraint Scheduling: Apply multiple scheduling techniques simultaneously for more controlled workload placement.
2. Workload Isolation and Distribution: Isolate critical workloads using taints and tolerations, while balancing pods using topology spread constraints.
3. Prioritized Scheduling: Assign priorities to critical workloads with priority classes and use preemption to reclaim resources.
4. Affinity for Performance: Use pod and node affinity to colocate or distribute pods for performance optimization.

---

# Practical Exercises: Combining Scheduling Techniques

---

### 1. Scenario: Deploying a Multi-Tier Application

**Objective:** Deploy a frontend, backend, and database application with optimized scheduling using combined techniques.

---

#### Step 1: Configure Priority Classes

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000
globalDefault: false
description: "High priority for database workloads."
```

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: medium-priority
value: 500
globalDefault: false
description: "Medium priority for backend workloads."
```

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 100
globalDefault: false
description: "Low priority for frontend workloads."
```

Apply the priority classes:

```bash
kubectl apply -f priority-classes.yaml
```

---

#### Step 2: Taint Nodes for Database Workloads

```bash
kubectl taint nodes <node-db> db=true:NoSchedule
```

---

#### Step 3: Deploy the Database with Tolerations and Node Affinity

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: db-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: database
  template:
    metadata:
      labels:
        app: database
    spec:
      priorityClassName: high-priority
      tolerations:
      - key: "db"
        operator: "Equal"
        value: "true"
        effect: "NoSchedule"
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: zone
                operator: In
                values:
                - zone-a
      containers:
      - name: postgres
        image: postgres
        resources:
          requests:
            cpu: "500m"
            memory: "1Gi"
          limits:
            cpu: "1"
            memory: "2Gi"
```

Apply the deployment:

```bash
kubectl apply -f db-deployment.yaml
```

---

#### Step 4: Deploy the Backend with Pod Affinity

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      priorityClassName: medium-priority
      affinity:
        podAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchLabels:
                app: database
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: backend
        image: nginx
        resources:
          requests:
            cpu: "300m"
            memory: "512Mi"
          limits:
            cpu: "500m"
            memory: "1Gi"
```

Apply the deployment:

```bash
kubectl apply -f backend-deployment.yaml
```

---

#### Step 5: Deploy the Frontend with Topology Spread Constraints

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      priorityClassName: low-priority
      topologySpreadConstraints:
      - maxSkew: 1
        topologyKey: "kubernetes.io/hostname"
        whenUnsatisfiable: DoNotSchedule
        labelSelector:
          matchLabels:
            app: frontend
      containers:
      - name: frontend
        image: nginx
        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"
          limits:
            cpu: "300m"
            memory: "512Mi"
```

Apply the deployment:

```bash
kubectl apply -f frontend-deployment.yaml
```

---

### 2. Verifying the Combined Scheduling

Check the placement of all pods:

```bash
kubectl get pods -o wide
```

Describe the pods to verify scheduler decisions:

```bash
kubectl describe pod <pod-name>
```

Monitor resource usage:

```bash
kubectl top nodes
kubectl top pods
```

---

# Cleanup

Remove all created resources:

```bash
kubectl delete deployment db-deployment backend-deployment frontend-deployment
kubectl delete priorityclass high-priority medium-priority low-priority
kubectl taint nodes <node-db> db-
```

---

### Best Practices for Combining Scheduling Techniques

1. **Balance Resource Isolation and Distribution:**
   - Use taints/tolerations for isolation and topology spread constraints for distribution.

2. **Use Priority Classes Judiciously:**
   - Avoid overusing high-priority classes to minimize preemption disruptions.

3. **Combine Affinity and Constraints:**
   - Leverage affinity rules to colocate or separate workloads intelligently.

4. **Continuously Monitor Resource Usage:**
   - Use monitoring tools to validate the effectiveness of scheduling strategies.

---

### 📝 Document Your Progress

In your `day68.md` file, include:

- Combined scheduling configurations.
- Observations on pod distribution and behavior.
- Lessons learned and improvements for workload optimization.

---

### 🎯 Outcome for Day 68

By the end of this session, you will:

1. Apply multiple advanced scheduling techniques together.
2. Optimize resource utilization and workload reliability.
3. Design resilient deployment strategies in Kubernetes.

---

### 🔗 Additional Resources

- [Kubernetes Scheduler Documentation](https://kubernetes.io/docs/concepts/scheduling-eviction/)
- [Affinity and Anti-Affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/affinity/)
- [Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)
- [Topology Spread Constraints](https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/)

---
