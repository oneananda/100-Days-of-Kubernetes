---

## Day 92: Kubernetes Multi-Cluster Federation with Submariner

### 📘 Overview

In today's session, you'll learn how to connect isolated Kubernetes clusters using **Submariner**—an open-source tool designed to enable seamless cross-cluster networking. Submariner simplifies the federation of clusters by establishing secure, encrypted tunnels between them, allowing services and workloads to communicate as if they were part of a single, unified network. This session covers installing and configuring Submariner, managing cross-cluster communication, and implementing security best practices for multi-cluster environments.

---

### Objectives

1. **Connect Isolated Clusters:**  
   - Deploy Submariner to federate multiple Kubernetes clusters across different networks.
   
2. **Manage Cross-Cluster Communication:**  
   - Configure service discovery and routing to enable seamless inter-cluster communication.
   
3. **Secure Multi-Cluster Networks:**  
   - Implement security measures such as encrypted tunnels and network policies to safeguard cross-cluster traffic.

---

### Key Concepts

1. **Submariner:**  
   - A tool that creates secure VPN tunnels between Kubernetes clusters, allowing them to communicate over private networks.
   
2. **Multi-Cluster Federation:**  
   - The process of connecting and managing multiple Kubernetes clusters, enabling centralized control and service discovery across environments.
   
3. **Cross-Cluster Communication:**  
   - Techniques and configurations that allow workloads in different clusters to access services reliably and efficiently.
   
4. **Security in Federated Networks:**  
   - Utilizing encryption, robust authentication, and network policies to protect data and communications across clusters.

---


### Practical Exercises: Implementing Multi-Cluster Federation with Submariner

#### 1. Installing Submariner

##### Step 1: Prepare Your Clusters  
Ensure that you have at least two Kubernetes clusters running in isolated networks. Verify connectivity and that each cluster has external access to allow tunnel establishment.

##### Step 2: Install the Submariner Operator  
Deploy the Submariner operator using your preferred method (Helm, OperatorHub, or manifests):
```bash
kubectl create namespace submariner-operator
kubectl apply -f https://github.com/submariner-io/submariner-operator/releases/download/v0.11.0/submariner-operator.yaml -n submariner-operator
```

##### Step 3: Install the Submariner CLI  
Download and install the `subctl` CLI tool to manage the federation process:
```bash
curl -Ls https://get.submariner.io | sh
sudo install subctl /usr/local/bin/
```

---

#### 2. Connecting Clusters with Submariner

##### Step 1: Broker Setup  
Designate one cluster as the broker for your multi-cluster environment and deploy the broker components:
```bash
subctl deploy-broker --kubeconfig <broker-kubeconfig>
```

##### Step 2: Join Clusters to the Broker  
Join each cluster to the broker by running the following command from each cluster:
```bash
subctl join --kubeconfig <cluster-kubeconfig> --clusterid <cluster-id> --natt=true <broker-info-file>
```
*Replace `<cluster-kubeconfig>`, `<cluster-id>`, and `<broker-info-file>` with appropriate values for your environment.*

##### Step 3: Validate Connectivity  
Test cross-cluster communication by deploying sample applications in different clusters and verifying connectivity:
```bash
kubectl run test-pod --image=busybox --restart=Never --command -- sleep 3600
kubectl exec test-pod -- ping -c 4 <service-ip-from-other-cluster>
```

---

#### 3. Securing Multi-Cluster Networks

##### Step 1: Enable Encrypted Tunnels  
Ensure that Submariner is configured to use encrypted tunnels (this is typically enabled by default with the `--natt` flag). Verify by checking the Submariner logs:
```bash
kubectl logs -n submariner-operator -l app=submariner-operator
```

##### Step 2: Apply Network Policies  
Implement network policies in each cluster to restrict and monitor cross-cluster traffic:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-submariner-traffic
  namespace: default
spec:
  podSelector: {}
  ingress:
    - from:
        - ipBlock:
            cidr: <submariner-network-cidr>
      ports:
        - protocol: TCP
          port: 443
```
*Replace `<submariner-network-cidr>` with the appropriate CIDR block used by Submariner.*

---


### Best Practices for Multi-Cluster Federation with Submariner

1. **Consistent Configuration:**  
   - Ensure that all clusters have similar network configurations and compatible versions of Kubernetes and Submariner.

2. **Secure Communication:**  
   - Always use encrypted tunnels and robust authentication methods to protect inter-cluster traffic.

3. **Monitor and Log:**  
   - Integrate monitoring tools like Prometheus and Grafana to observe network performance and detect anomalies.

4. **Test in Staging:**  
   - Validate all configurations and conduct thorough testing in a staging environment before applying changes to production clusters.

5. **Documentation and Versioning:**  
   - Maintain clear documentation of your multi-cluster setup and use version control for configuration files.

---


### 📝 Document Your Progress

In your `day92.md` file, include:
- Detailed steps and commands for installing and configuring Submariner.
- Screenshots or logs demonstrating successful connection and communication between clusters.
- Observations on the performance and security of cross-cluster traffic.
- Any challenges encountered and how they were resolved.

---

### 🎯 Outcome for Day 92

By the end of this session, you will:
1. Successfully connect isolated Kubernetes clusters using Submariner.
2. Manage cross-cluster communication, enabling service discovery and network routing between clusters.
3. Implement and verify security measures to protect multi-cluster networks.

---

### 🔗 Additional Resources

- [Submariner Documentation](https://submariner.io/documentation/)
- [Submariner GitHub Repository](https://github.com/submariner-io/submariner)
- [Multi-Cluster Networking Best Practices](https://kubernetes.io/docs/concepts/cluster-administration/multi-cluster/)
- [Kubernetes Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

---