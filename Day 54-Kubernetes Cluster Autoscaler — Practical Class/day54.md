---

## Day 54: Kubernetes Cluster Autoscaler — Practical Class

### 📘 Overview

The **Cluster Autoscaler** in Kubernetes automatically adjusts the size of a cluster by adding or removing nodes based on resource demands. It ensures efficient resource utilization while maintaining workload performance.

This session focuses on configuring the Cluster Autoscaler, testing its scaling behavior, and understanding its integration with cloud providers.

---

### Objectives

1. Understand the purpose and functionality of the Cluster Autoscaler.
2. Configure the Cluster Autoscaler for a Kubernetes cluster.
3. Simulate workloads to trigger scaling events.
4. Monitor and analyze the scaling behavior of the Cluster Autoscaler.

---

### Key Concepts

1. **Cluster Autoscaler**:
   - Automatically adjusts the number of nodes in a cluster.
   - Scales up when there are pending pods that cannot be scheduled due to insufficient resources.
   - Scales down when nodes are underutilized and pods can be rescheduled on other nodes.

2. **Cloud Provider Integration**:
   - Works with managed Kubernetes services like AWS EKS, Google GKE, and Azure AKS.

3. **Node Groups**:
   - Logical grouping of nodes for scaling operations.

4. **Scaling Policies**:
   - Configurable thresholds for scaling up and scaling down nodes.

---

### Practical Exercises

---

### 1. Setting Up the Cluster Autoscaler

#### Step 1: Install the Cluster Autoscaler
For a managed Kubernetes service (e.g., AWS EKS), use the following command to install the Cluster Autoscaler:
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/deployments/cluster-autoscaler-autodiscover.yaml
```

#### Step 2: Configure the Autoscaler
Edit the deployment to configure the autoscaler for your cloud provider. For example, in AWS:
```bash
kubectl edit deployment cluster-autoscaler -n kube-system
```

Update the command section to include the `--nodes` flag:
```yaml
containers:
- name: cluster-autoscaler
  command:
  - ./cluster-autoscaler
  - --v=4
  - --stderrthreshold=info
  - --cloud-provider=aws
  - --nodes=1:10:<node-group-name>
  - --skip-nodes-with-local-storage=false
  - --balance-similar-node-groups
```

---

### 2. Testing Scaling Behavior

#### Step 1: Deploy a Resource-Intensive Workload
Create a `scaling-test.yaml` file to simulate high resource usage:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cpu-intensive
spec:
  replicas: 5
  selector:
    matchLabels:
      app: cpu-intensive
  template:
    metadata:
      labels:
        app: cpu-intensive
    spec:
      containers:
      - name: stress
        image: polinux/stress
        args:
        - "--cpu"
        - "2"
        - "--timeout"
        - "600s"
        resources:
          requests:
            cpu: "500m"
            memory: "256Mi"
```

Apply the deployment:
```bash
kubectl apply -f scaling-test.yaml
```

#### Step 2: Monitor the Autoscaler
View pending pods and autoscaling events:
```bash
kubectl get pods
kubectl logs -f deployment/cluster-autoscaler -n kube-system
```

Verify that new nodes are being added to the cluster:
```bash
kubectl get nodes
```

---

### 3. Testing Scale-Down Behavior

#### Step 1: Reduce the Workload
Scale down the deployment:
```bash
kubectl scale deployment cpu-intensive --replicas=1
```

#### Step 2: Observe Node Scaling
Monitor node activity as underutilized nodes are removed:
```bash
kubectl get nodes
kubectl logs -f deployment/cluster-autoscaler -n kube-system
```

---

### 4. Cleanup

Remove all resources:
```bash
kubectl delete -f scaling-test.yaml
kubectl delete deployment cluster-autoscaler -n kube-system
```

---

### Best Practices for Cluster Autoscaler

1. **Set Node Group Limits**:
   - Configure `--nodes` to define the minimum and maximum nodes for each node group.

2. **Node Balancing**:
   - Use the `--balance-similar-node-groups` flag to evenly distribute pods across node groups.

3. **Avoid Local Storage Issues**:
   - Avoid using nodes with local storage unless absolutely necessary (`--skip-nodes-with-local-storage`).

4. **Monitoring**:
   - Use logs and monitoring tools to track autoscaling behavior and resource usage.

5. **Right-Sizing Workloads**:
   - Ensure that resource requests and limits are accurately set for workloads to trigger scaling effectively.

---
