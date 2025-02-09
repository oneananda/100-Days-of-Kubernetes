---

## Day 95: Kubernetes High Availability and Resilience

### 📘 Overview

High availability (HA) and resilience are critical for ensuring that your Kubernetes clusters can withstand failures and continue to operate seamlessly. In this session, you'll learn how to design highly available Kubernetes clusters, configure the control plane and etcd for HA, and test the resilience of your setup under various failure scenarios. This hands-on approach will prepare you to build robust clusters that minimize downtime and maintain service continuity.

---


### Objectives

1. **Design Highly Available Clusters:**  
   - Understand architectural best practices for achieving high availability in Kubernetes deployments.
   
2. **Configure HA for Control Plane and etcd:**  
   - Learn to set up redundant control plane nodes and secure the etcd data store to ensure continuity and reliability.
   
3. **Test Resilience Under Failures:**  
   - Execute failure simulations and observe cluster behavior to validate your HA configurations and resilience strategies.

---

### Key Concepts

1. **High Availability (HA):**  
   - Designing systems that remain operational even when some components fail.
   - In Kubernetes, this involves multiple control plane nodes and a fault-tolerant etcd cluster.

2. **Control Plane Redundancy:**  
   - Distributing API server, scheduler, and controller-manager across multiple nodes.
   - Utilizing load balancers to distribute requests among control plane nodes.

3. **etcd Resilience:**  
   - Configuring etcd as a clustered data store with quorum-based replication.
   - Regular backups and proper resource allocation to safeguard cluster state.

4. **Failure Testing:**  
   - Simulating component failures to assess the effectiveness of HA configurations.
   - Tools and techniques for chaos testing and disaster recovery validation.

---

### Practical Exercises: Building and Testing an HA Kubernetes Cluster

#### 1. Designing Highly Available Clusters

##### Step 1: Architectural Considerations
- Plan for at least three control plane nodes to ensure quorum.
- Design a network architecture that supports redundancy and load balancing.
- Ensure that etcd is deployed as a multi-node cluster with an odd number of nodes (typically three or five) for fault tolerance.

##### Step 2: Infrastructure Setup
- Use tools like Terraform, Ansible, or Kubernetes Cluster API to provision multiple nodes.
- Configure a load balancer (e.g., HAProxy, NGINX, or cloud-managed LB) to route traffic to the control plane nodes.

---

#### 2. Configuring HA for Control Plane and etcd

##### Step 1: Deploying the Control Plane
- Set up multiple control plane nodes with Kubernetes components:
  ```bash
  # Example using kubeadm to initialize a multi-node control plane:
  kubeadm init --control-plane-endpoint "<LOAD_BALANCER_DNS>:6443" --upload-certs
  ```
- Join additional control plane nodes using the generated token and certificate key:
  ```bash
  kubeadm join <LOAD_BALANCER_DNS>:6443 --token <token> \
    --discovery-token-ca-cert-hash sha256:<hash> \
    --control-plane --certificate-key <certificate-key>
  ```

##### Step 2: Configuring etcd for Resilience
- Deploy etcd in a clustered mode:
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: etcd-0
    labels:
      component: etcd
  spec:
    containers:
      - name: etcd
        image: quay.io/coreos/etcd:v3.4.13
        command:
          - /usr/local/bin/etcd
          - --name=etcd-0
          - --data-dir=/var/lib/etcd
          - --initial-advertise-peer-urls=http://etcd-0.example.com:2380
          - --listen-peer-urls=http://0.0.0.0:2380
          - --listen-client-urls=http://0.0.0.0:2379
          - --advertise-client-urls=http://etcd-0.example.com:2379
          - --initial-cluster-token=etcd-cluster-1
          - --initial-cluster=etcd-0=http://etcd-0.example.com:2380,etcd-1=http://etcd-1.example.com:2380,etcd-2=http://etcd-2.example.com:2380
          - --initial-cluster-state=new
  ```
- Repeat for additional etcd nodes (etcd-1, etcd-2) ensuring proper configuration for clustering.
- Implement regular backup and restore strategies for etcd data.

---

#### 3. Testing Resilience Under Failures

##### Step 1: Simulate Control Plane Failures
- Manually stop or isolate one or more control plane nodes:
  ```bash
  # Example: Stop the API server on one control plane node
  sudo systemctl stop kube-apiserver
  ```
- Observe the behavior using:
  ```bash
  kubectl get nodes
  kubectl get pods --all-namespaces
  ```
- Verify that the load balancer routes traffic to healthy control plane nodes.

##### Step 2: Simulate etcd Failures
- Introduce a failure scenario by stopping one etcd node:
  ```bash
  sudo systemctl stop etcd
  ```
- Monitor the etcd cluster health:
  ```bash
  ETCDCTL_API=3 etcdctl endpoint health --endpoints=<list_of_etcd_endpoints>
  ```
- Validate that the cluster maintains quorum and that recovery processes are effective.

##### Step 3: Use Chaos Engineering Tools
- Integrate chaos testing tools (e.g., LitmusChaos) to automate failure injection:
  ```bash
  # Run a pod deletion experiment to simulate control plane pod failures
  kubectl apply -f chaos-experiment.yaml
  ```
- Analyze system logs and metrics to assess the resilience and recovery of the cluster.

---
