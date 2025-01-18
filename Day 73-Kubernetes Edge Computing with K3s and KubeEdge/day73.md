---

## Day 73: Kubernetes Edge Computing with K3s and KubeEdge

### 📘 Overview

**Edge computing** brings compute, storage, and networking resources closer to end-users and devices, reducing latency and improving performance for real-time applications. Lightweight Kubernetes distributions like **K3s** and frameworks like **KubeEdge** enable Kubernetes to run efficiently on edge devices. This session covers deploying K3s for edge computing, introducing KubeEdge for IoT workloads, and managing edge nodes with intermittent connectivity.

---

### Objectives

1. Deploy lightweight Kubernetes clusters using **K3s** for edge devices.  
2. Understand **KubeEdge** and its role in managing IoT workloads.  
3. Manage edge nodes and handle intermittent connectivity between cloud and edge clusters.  

---

### Key Concepts

1. **K3s:**  
   - A lightweight, production-grade Kubernetes distribution designed for IoT and edge computing.  
   - Simplified deployment with a reduced resource footprint.  

2. **KubeEdge:**  
   - Extends Kubernetes to edge devices, enabling deployment and management of workloads on edge nodes.  
   - Supports offline operation and edge-cloud synchronization.  

3. **Edge Node Management:**  
   - Techniques for managing and monitoring edge nodes, especially in environments with unreliable network connections.  

---


### Practical Exercises: Deploying K3s and KubeEdge

---

#### 1. Deploying Lightweight Kubernetes with K3s

##### Step 1: Install K3s on the Edge Node
Run the following command on the edge device (Raspberry Pi, VM, or server):
```bash
curl -sfL https://get.k3s.io | sh -
```

Verify the installation:
```bash
sudo k3s kubectl get nodes
```

##### Step 2: Access K3s from a Remote System
Copy the K3s kubeconfig file to your local machine:
```bash
sudo cat /etc/rancher/k3s/k3s.yaml
```

Set the `KUBECONFIG` environment variable on the local system:
```bash
export KUBECONFIG=/path/to/k3s.yaml
kubectl get nodes
```

---

#### 2. Introduction to KubeEdge for IoT Workloads

##### Step 1: Install KubeEdge on the Cloud Master Node
Download and install KubeEdge:
```bash
curl -LO https://github.com/kubeedge/kubeedge/releases/download/v1.13.0/keadm-v1.13.0-linux-amd64.tar.gz
tar -xvf keadm-v1.13.0-linux-amd64.tar.gz
sudo mv keadm-v1.13.0-linux-amd64/keadm /usr/local/bin/
```

Initialize the cloud component:
```bash
sudo keadm init --kube-config=/etc/rancher/k3s/k3s.yaml
```

##### Step 2: Install KubeEdge on the Edge Node
Generate a join token on the cloud master:
```bash
sudo keadm gettoken
```

On the edge node, join the cluster:
```bash
sudo keadm join --cloudcore-ipport=<cloud-master-ip>:10000 --token=<token>
```

Verify the connection:
```bash
kubectl get nodes
```

---

#### 3. Deploying Workloads to Edge Nodes

##### Step 1: Deploy an IoT Application
Create a `sensor-app.yaml` file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sensor-app
  labels:
    app: sensor
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sensor
  template:
    metadata:
      labels:
        app: sensor
    spec:
      nodeSelector:
        node-role.kubernetes.io/edge: ""
      containers:
      - name: sensor
        image: eclipse-mosquitto
        ports:
        - containerPort: 1883
```

Apply the deployment:
```bash
kubectl apply -f sensor-app.yaml
```

##### Step 2: Verify Deployment on Edge Node
Check if the pod is running on the edge node:
```bash
kubectl get pods -o wide
```

**Expected Result:** The `sensor-app` should be running on the edge node.

---

#### 4. Handling Intermittent Connectivity

##### Step 1: Simulate Network Disruption
Disconnect the edge node network (disable network interface or unplug cable).

##### Step 2: Observe Offline Operation
Check pod status on the cloud master:
```bash
kubectl get pods
```

**Expected Behavior:** The edge node continues running workloads without interruption.

##### Step 3: Reconnect the Edge Node
Reconnect the network and verify the node status:
```bash
kubectl get nodes
```

**Expected Result:** The edge node should automatically reconnect to the cloud master.

---

#### Cleanup

Remove K3s and KubeEdge:
```bash
sudo /usr/local/bin/k3s-uninstall.sh
sudo keadm reset
kubectl delete -f sensor-app.yaml
```

---


### Best Practices for Edge Computing with Kubernetes

1. **Use Lightweight Distributions for Edge Devices:**  
   - Deploy K3s to minimize resource usage on edge nodes.  

2. **Ensure Resiliency with KubeEdge:**  
   - Configure KubeEdge to handle offline workloads during connectivity loss.  

3. **Implement Node Health Monitoring:**  
   - Monitor edge node health and availability using built-in Kubernetes probes.  

4. **Optimize Network Usage:**  
   - Use local caching and data processing to minimize cloud dependency.  

---


### 📝 Document Your Progress

In your `day73.md` file, include:  
- Steps for installing and configuring K3s and KubeEdge.  
- Deployment results of IoT workloads.  
- Observations on handling intermittent connectivity.  

---

### 🎯 Outcome for Day 73

By the end of this session, you will:  
1. Deploy K3s on edge devices for lightweight Kubernetes management.  
2. Set up KubeEdge for managing IoT workloads.  
3. Manage edge nodes effectively and handle network disruptions.  

---

### 🔗 Additional Resources

- [K3s Official Documentation](https://k3s.io/)  
- [KubeEdge Documentation](https://kubeedge.io/en/docs/)  
- [Edge Computing with Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/networking/)  

---