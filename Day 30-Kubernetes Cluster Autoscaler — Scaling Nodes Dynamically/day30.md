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


### Hands-On with Cluster Autoscaler

---

### 1. Enabling Cluster Autoscaler

#### Deploy Cluster Autoscaler:
Use the following command to deploy the Cluster Autoscaler on a cloud provider:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/deploy/cluster-autoscaler-autodiscover.yaml
```

#### Verify Deployment:
```bash
kubectl get pods -n kube-system | grep cluster-autoscaler
```

---

### 2. Configuring Cluster Autoscaler

#### Edit the Deployment:
Modify the Cluster Autoscaler deployment to include the correct cloud provider and options:

```bash
kubectl edit deployment cluster-autoscaler -n kube-system
```

Add flags such as:
```yaml
command:
  - ./cluster-autoscaler
  - --cloud-provider=<cloud-provider>
  - --namespace=kube-system
  - --scale-down-utilization-threshold=0.5
  - --scale-down-unneeded-time=10m
```

---

### 3. Simulating Scaling Scenarios

#### Scale-Up Test:
1. Deploy a workload that exceeds the cluster's current capacity:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: scale-up-test
   spec:
     replicas: 20
     selector:
       matchLabels:
         app: busybox
     template:
       metadata:
         labels:
           app: busybox
       spec:
         containers:
         - name: busybox
           image: busybox
           resources:
             requests:
               memory: "500Mi"
               cpu: "200m"
           command: ["sleep", "3600"]
   ```

   Apply the workload:
   ```bash
   kubectl apply -f scale-up-test.yaml
   ```

2. Observe the Cluster Autoscaler adding nodes:
   ```bash
   kubectl get nodes
   kubectl describe pod <pending-pod-name>
   ```

#### Scale-Down Test:
1. Scale down the deployment to reduce resource usage:
   ```bash
   kubectl scale deployment scale-up-test --replicas=5
   ```

2. Observe Cluster Autoscaler removing underutilized nodes:
   ```bash
   kubectl get nodes
   kubectl logs -n kube-system <cluster-autoscaler-pod>
   ```

---

### 4. Best Practices for Cluster Autoscaler

1. **Use Resource Requests**:
   - Define CPU and memory requests in pod specs to enable accurate scaling.

2. **Set Node Group Limits**:
   - Configure minimum and maximum limits for node groups to avoid over-provisioning.

3. **Monitor Scaling Activity**:
   - Use tools like Prometheus and Grafana to monitor node scaling events.

4. **Combine with HPA**:
   - Use Horizontal Pod Autoscaler with Cluster Autoscaler for efficient workload and node scaling.

---


### 📝 Document Your Progress

In your `day30.md` file, record:
- Steps for deploying and configuring Cluster Autoscaler.
- YAML configurations for test workloads.
- Observations during scale-up and scale-down scenarios.
- Insights on optimizing cluster scaling for cost and performance.

---

### 🎯 Outcome for Day 30

By the end of Day 30, you should:
1. Understand the role of Kubernetes Cluster Autoscaler in managing node scaling.
2. Deploy and configure Cluster Autoscaler in your environment.
3. Simulate scaling scenarios and observe how Cluster Autoscaler responds.
4. Apply best practices to optimize resource usage and minimize costs.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Cluster Autoscaler](https://kubernetes.io/docs/tasks/administer-cluster/cluster-management/#cluster-autoscaler)
- [Autoscaler GitHub Repository](https://github.com/kubernetes/autoscaler)

---