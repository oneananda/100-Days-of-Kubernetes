---

## Day 27: Kubernetes Pod Disruption Budgets — Ensuring Application Availability

### 📘 Overview

**Pod Disruption Budgets (PDBs)** in Kubernetes help maintain application availability during voluntary disruptions, such as node maintenance or rolling updates. A PDB specifies the minimum or maximum number of pods that must remain available, ensuring critical applications are not entirely disrupted.

---

### What is a Pod Disruption Budget?

1. **Purpose**:
   - Define limits for voluntary disruptions (e.g., draining nodes or rolling updates).
   - Ensure the application remains operational while accommodating maintenance tasks.

2. **Voluntary vs. Involuntary Disruptions**:
   - **Voluntary**: User-initiated actions like node drain or scaling.
   - **Involuntary**: Unexpected failures like crashes or hardware issues.

3. **PDB Specification**:
   - `minAvailable`: Minimum number of pods that must remain running.
   - `maxUnavailable`: Maximum number of pods that can be unavailable at a time.

---


### Key Concepts

1. **Integration with Controllers**:
   - PDBs work with ReplicaSets, Deployments, StatefulSets, and other controllers that manage pods.

2. **No Guarantee for Involuntary Disruptions**:
   - PDBs do not guarantee pod availability during involuntary disruptions like crashes or network failures.

3. **Namespace-Specific**:
   - PDBs are defined per namespace and target specific pods using label selectors.

---

### Hands-On with Pod Disruption Budgets

---

### 1. Creating a Pod Disruption Budget

#### Define a PDB:
Save the following YAML as `pdb.yaml`:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: example-pdb
  namespace: default
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-app
```

#### Apply the PDB:
```bash
kubectl apply -f pdb.yaml
```

#### Verify the PDB:
```bash
kubectl get pdb
kubectl describe pdb example-pdb
```

---

### 2. Simulating a Disruption

#### Deploy a Sample Application:
Save the following as `deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: nginx
        image: nginx
```

#### Apply the Deployment:
```bash
kubectl apply -f deployment.yaml
```

#### Drain a Node:
Identify a node and drain it:
```bash
kubectl get nodes
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
```

Observe that Kubernetes prevents more than one pod from being disrupted, as specified by the PDB.

---

### 3. Updating a Pod Disruption Budget

#### Modify the PDB:
```bash
kubectl edit pdb example-pdb
```

Change `minAvailable` or `maxUnavailable` as needed and save the changes.

#### Verify the Updated PDB:
```bash
kubectl describe pdb example-pdb
```

---

### 4. Best Practices for Using PDBs

1. **Application Awareness**:
   - Use PDBs to protect critical applications with high availability requirements.

2. **Balance with Rolling Updates**:
   - Ensure PDB settings do not overly restrict rolling updates or maintenance tasks.

3. **Granular Control**:
   - Create PDBs for different tiers or components of an application (e.g., frontend, backend).

4. **Cluster Monitoring**:
   - Monitor disruptions and ensure the cluster has enough resources to meet PDB requirements.

---

### 📝 Document Your Progress

In your `day27.md` file, record:
- YAML configurations for PDBs and deployments.
- Observations during node drains or other voluntary disruptions.
- Insights on how PDBs enhance application availability and resilience.

---

### 🎯 Outcome for Day 27

By the end of Day 27, you should:
1. Understand the role of Pod Disruption Budgets in Kubernetes.
2. Create and manage PDBs to ensure application availability.
3. Simulate and handle disruptions with PDBs in place.
4. Apply best practices for using PDBs in production.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Pod Disruption Budgets](https://kubernetes.io/docs/tasks/run-application/configure-pdb/)
- [Ensuring Application Availability](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)

---