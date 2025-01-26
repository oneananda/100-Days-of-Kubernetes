---

## Day 81: Kubernetes Multi-Cluster Networking with Cilium

### 📘 Overview

Cilium is a powerful networking and security tool for Kubernetes based on **eBPF** (Extended Berkeley Packet Filter). It provides efficient networking, advanced observability, and robust security for multi-cluster environments. This session focuses on setting up multi-cluster connectivity using Cilium, leveraging eBPF for advanced networking, and implementing observability and security best practices.

---

### Objectives

1. Understand the fundamentals of Cilium and eBPF in Kubernetes networking.  
2. Set up multi-cluster networking using Cilium.  
3. Explore advanced network observability and implement security policies for Kubernetes clusters.  

---

### Key Concepts

1. **Cilium and eBPF:**  
   - **Cilium:** A networking and security solution leveraging eBPF for high performance and scalability.  
   - **eBPF:** A Linux kernel technology enabling efficient packet processing and programmability.  

2. **Multi-Cluster Networking:**  
   - Connects multiple Kubernetes clusters, allowing seamless communication between workloads.  

3. **Advanced Observability:**  
   - Tools and techniques to monitor network flows, troubleshoot issues, and analyze performance.  

4. **Network Security:**  
   - Fine-grained security policies for workload isolation and traffic filtering.  

---


### Practical Exercises: Setting Up Cilium for Multi-Cluster Networking

---

#### 1. Installing Cilium

##### Step 1: Install Cilium CLI
Download and install the Cilium CLI:
```bash
curl -L --remote-name https://github.com/cilium/cilium-cli/releases/latest/download/cilium-linux-amd64
chmod +x cilium-linux-amd64
sudo mv cilium-linux-amd64 /usr/local/bin/cilium
```

##### Step 2: Deploy Cilium in Each Cluster
Install Cilium in Cluster 1:
```bash
cilium install
cilium status
```

Repeat the installation for Cluster 2 and other clusters.

---

#### 2. Enabling Multi-Cluster Connectivity

##### Step 1: Configure Cluster IDs
Assign unique cluster IDs in each cluster:
```bash
cilium config set cluster-id <cluster-id>
```

##### Step 2: Enable Cluster Mesh
Install the Cilium Cluster Mesh add-on in each cluster:
```bash
helm repo add cilium https://helm.cilium.io/
helm repo update
helm install cilium cilium/cilium --namespace kube-system --set cluster.name=<cluster-name> --set cluster.id=<cluster-id> --set clusterMesh.enabled=true
```

##### Step 3: Establish Connectivity
Generate the cluster-mesh configuration:
```bash
cilium clustermesh enable
```

Export the configuration and share it between clusters:
```bash
cilium clustermesh status
```

---

#### 3. Advanced Network Observability

##### Step 1: Monitor Network Flows
Enable Hubble for network observability:
```bash
cilium hubble enable
```

Access Hubble UI:
```bash
cilium hubble ui
```

##### Step 2: Analyze Flows
View real-time network flows:
```bash
cilium hubble observe
```

Filter specific flows by namespace or workload:
```bash
cilium hubble observe --namespace <namespace-name>
```

---

#### 4. Implementing Network Security Policies

##### Step 1: Create a Network Policy
Define a Cilium NetworkPolicy to restrict traffic:
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: allow-frontend-to-backend
spec:
  endpointSelector:
    matchLabels:
      app: backend
  ingress:
  - fromEndpoints:
    - matchLabels:
        app: frontend
```

Apply the policy:
```bash
kubectl apply -f network-policy.yaml
```

##### Step 2: Validate the Policy
Test the connectivity:
```bash
kubectl exec -n <frontend-pod> -- curl <backend-service>
```

---

### Best Practices for Multi-Cluster Networking with Cilium

1. **Use Unique Cluster IDs:**  
   - Assign unique IDs to each cluster to avoid conflicts in multi-cluster setups.  

2. **Leverage Hubble Observability:**  
   - Monitor network traffic in real-time to detect and resolve issues.  

3. **Define Fine-Grained Security Policies:**  
   - Isolate workloads and limit traffic to required paths using Cilium Network Policies.  

4. **Implement Encryption:**  
   - Use Cilium’s encryption features to secure traffic between clusters.  

5. **Regularly Test Connectivity:**  
   - Validate multi-cluster connectivity after changes to the network or workloads.

---


### 📝 Document Your Progress

In your `day81.md` file, include:  
- Steps for setting up Cilium and enabling multi-cluster connectivity.  
- Observations from using Hubble for network observability.  
- Configuration and results of implementing network security policies.  

---

### 🎯 Outcome for Day 81

By the end of this session, you will:  
1. Deploy Cilium in Kubernetes clusters and enable multi-cluster networking.  
2. Use Hubble for advanced observability of network flows.  
3. Implement and validate security policies for improved workload isolation.  

---

### 🔗 Additional Resources

- [Cilium Documentation](https://docs.cilium.io/)  
- [Hubble Observability](https://docs.cilium.io/en/stable/gettingstarted/hubble/)  
- [Multi-Cluster Networking](https://docs.cilium.io/en/stable/gettingstarted/clustermesh/)  
- [eBPF Documentation](https://ebpf.io/)  

---