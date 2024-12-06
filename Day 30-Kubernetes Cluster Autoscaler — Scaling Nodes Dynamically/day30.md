---

## Day 30: Kubernetes Cluster Autoscaler — Scaling Nodes Dynamically

### 📘 Overview

**Kubernetes Cluster Autoscaler** dynamically adjusts the number of nodes in a cluster to match the resource demands of the workloads. It ensures that your cluster has sufficient capacity to run all scheduled pods while minimizing costs by scaling down unused nodes.

---


### What is Cluster Autoscaler?

1. **Purpose**:
   - Scale the cluster up when pods cannot be scheduled due to insufficient resources.
   - Scale the cluster down when nodes are underutilized and pods can be scheduled elsewhere.

2. **Components**:
   - Works with cloud provider APIs (e.g., AWS, GCP, Azure) to add or remove nodes.

3. **Supported Scenarios**:
   - Add nodes when workloads exceed current node capacity.
   - Remove nodes when they are underutilized and workloads can be rescheduled.

---

### Key Concepts

1. **Node Groups**:
   - A group of nodes managed by the autoscaler (e.g., ASGs on AWS, Instance Groups on GCP).

2. **Scaling Policies**:
   - **Scale-Up**: Triggered when pending pods cannot be scheduled due to resource constraints.
   - **Scale-Down**: Triggered when nodes are underutilized and pods can be rescheduled.

3. **Pod Disruption Budgets (PDBs)**:
   - Ensures applications are not disrupted during scale-down operations.

4. **Configuration**:
   - Define autoscaler behavior using flags like `--scale-down-utilization-threshold`.

---