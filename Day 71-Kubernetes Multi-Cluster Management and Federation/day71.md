---

## Day 71: Kubernetes Multi-Cluster Management and Federation

### 📘 Overview

As organizations scale, managing multiple Kubernetes clusters across regions and cloud providers becomes essential for high availability, disaster recovery, and workload distribution. **Kubernetes Federation (KubeFed)** provides a unified way to manage resources across multiple clusters. This session covers the fundamentals of Kubernetes Federation, multi-cluster management strategies, and cross-cluster service discovery and failover.

---

### Objectives

1. Understand Kubernetes Cluster Federation (KubeFed) and its role in multi-cluster management.  
2. Learn how to manage multiple Kubernetes clusters across regions and cloud providers.  
3. Implement cross-cluster service discovery and failover strategies.  

---

### Key Concepts

1. **Kubernetes Federation (KubeFed):**  
   - Enables the deployment and management of Kubernetes resources across multiple clusters.  
   - Provides centralized control over multi-cluster environments.  

2. **Multi-Cluster Management:**  
   - Techniques and tools for managing clusters in different regions/cloud providers.  
   - Examples: KubeFed, Rancher, Google Anthos, and Amazon EKS-A.  

3. **Cross-Cluster Service Discovery and Failover:**  
   - Mechanisms to enable services to communicate across clusters.  
   - Ensures availability and redundancy through automatic failover.  

---

### Practical Exercises: Kubernetes Federation and Multi-Cluster Management

---

#### 1. Installing Kubernetes Federation (KubeFed)

##### Step 1: Install KubeFed CLI
Install the KubeFed CLI (`kubefedctl`):
```bash
curl -LO https://github.com/kubernetes-sigs/kubefed/releases/download/v0.9.0/kubefedctl-$(uname -s)-amd64.tgz
tar -xzf kubefedctl-*.tgz
sudo mv kubefedctl /usr/local/bin/
```

Verify the installation:
```bash
kubefedctl version
```

##### Step 2: Deploy KubeFed Control Plane
Install KubeFed in the host cluster:
```bash
kubefedctl join cluster1 \
  --host-cluster-context cluster1 \
  --add-to-registry \
  --v=2
```

Verify the federation control plane:
```bash
kubectl get pods -n kube-federation-system
```

---

#### 2. Joining Multiple Clusters

##### Step 1: Configure Contexts for Clusters
Ensure both clusters (`cluster1` and `cluster2`) are configured:
```bash
kubectl config use-context cluster1
kubectl config use-context cluster2
```

##### Step 2: Join Additional Clusters to the Federation
Join `cluster2` to the federation:
```bash
kubefedctl join cluster2 \
  --host-cluster-context cluster1 \
  --v=2
```

Verify that both clusters are joined:
```bash
kubectl get kubefedclusters
```

---

#### 3. Deploying Federated Resources

##### Step 1: Define a Federated Deployment
Create a `federated-nginx.yaml`:
```yaml
apiVersion: types.kubefed.io/v1beta1
kind: FederatedDeployment
metadata:
  name: nginx
  namespace: default
spec:
  template:
    metadata:
      labels:
        app: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
```

Apply the federated deployment:
```bash
kubectl apply -f federated-nginx.yaml
```

##### Step 2: Verify Deployment Across Clusters
Check deployment status in both clusters:
```bash
kubectl --context=cluster1 get pods
kubectl --context=cluster2 get pods
```

---

#### 4. Implementing Cross-Cluster Service Discovery

##### Step 1: Define a Federated Service
Create a `federated-service.yaml`:
```yaml
apiVersion: types.kubefed.io/v1beta1
kind: FederatedService
metadata:
  name: nginx-service
  namespace: default
spec:
  template:
    metadata:
      labels:
        app: nginx
    spec:
      selector:
        app: nginx
      ports:
      - protocol: TCP
        port: 80
        targetPort: 80
```

Apply the service:
```bash
kubectl apply -f federated-service.yaml
```

##### Step 2: Enable DNS-Based Service Discovery
Install CoreDNS for cross-cluster discovery:
```bash
kubectl apply -f https://k8s.io/examples/admin/dns/coredns.yaml
```

Query the service:
```bash
nslookup nginx-service.default.svc.cluster.local
```

**Expected Result:** Service discovery works across clusters.

---

#### 5. Configuring Failover Between Clusters

##### Step 1: Define Failover Policy
Create a `failover-policy.yaml`:
```yaml
apiVersion: types.kubefed.io/v1beta1
kind: FederatedDeployment
metadata:
  name: nginx-failover
  namespace: default
spec:
  overrides:
  - clusterName: cluster1
    clusterOverrides:
    - path: "/spec/replicas"
      value: 2
  - clusterName: cluster2
    clusterOverrides:
    - path: "/spec/replicas"
      value: 0
```

Apply the failover policy:
```bash
kubectl apply -f failover-policy.yaml
```

##### Step 2: Simulate Cluster Failure
Drain nodes in `cluster1` to simulate failure:
```bash
kubectl --context=cluster1 drain <node-name> --ignore-daemonsets --delete-local-data
```

Verify that pods are shifted to `cluster2`:
```bash
kubectl --context=cluster2 get pods
```

---

#### Cleanup

Remove the federated resources:
```bash
kubectl delete -f federated-nginx.yaml
kubectl delete -f federated-service.yaml
kubectl delete -f failover-policy.yaml
kubefedctl unjoin cluster2 --host-cluster-context cluster1
kubefedctl unjoin cluster1 --host-cluster-context cluster1
```

---
