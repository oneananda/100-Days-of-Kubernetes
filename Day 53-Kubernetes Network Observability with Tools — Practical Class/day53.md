---

## Day 53: Kubernetes Network Observability with Tools — Practical Class

### 📘 Overview

Networking is a crucial part of Kubernetes, and understanding traffic flows within the cluster helps ensure performance, security, and troubleshooting. This session focuses on using tools like **KubeFlow**, **Weave Scope**, and **Wireshark** to gain insights into network traffic, connections, and bottlenecks in Kubernetes.

---

### Objectives

1. Understand the importance of network observability in Kubernetes.
2. Deploy tools for visualizing network traffic and dependencies.
3. Monitor traffic flows and troubleshoot connectivity issues.
4. Analyze network performance metrics and bottlenecks.

---

### Key Concepts

1. **Network Observability**:
   - Involves monitoring, visualizing, and analyzing network traffic and dependencies within a Kubernetes cluster.

2. **Tools**:
   - **Weave Scope**: Real-time visualization of container and network interactions.
   - **Wireshark**: Packet capture and analysis tool for deep network insights.
   - **KubeFlow**: Observability tools for Kubernetes networking and flows.

3. **Network Policies**:
   - Define and enforce rules about how pods communicate with each other or external resources.

---

### Practical Exercises

---

### 1. Setting Up Weave Scope for Network Observability

#### Step 1: Deploy Weave Scope
Run the following command to deploy Weave Scope in the cluster:
```bash
kubectl apply -f "https://cloud.weave.works/k8s/scope.yaml"
```

Verify the deployment:
```bash
kubectl get pods -n weave
```

#### Step 2: Access Weave Scope
Forward the port to access the Weave Scope dashboard:
```bash
kubectl port-forward -n weave svc/weave-scope-app 4040:80
```

Open the dashboard in your browser at `http://localhost:4040`.

---

### 2. Visualizing Traffic Flows

#### Step 1: Deploy a Sample Application
Create a `network-observability.yaml` file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
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
      containers:
      - name: backend
        image: busybox
        command: ["sh", "-c", "while true; do nc -l -p 8080; done"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  selector:
    app: frontend
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

Apply the configuration:
```bash
kubectl apply -f network-observability.yaml
```

#### Step 2: Generate Traffic
Send traffic from the frontend to the backend:
```bash
kubectl exec -it <frontend-pod-name> -- sh -c "curl backend-service:8080"
```

Observe the traffic flow in the Weave Scope dashboard.

---

### 3. Analyzing Network Packets with Wireshark

#### Step 1: Capture Network Packets
Identify the node IP and use `tcpdump` to capture traffic:
```bash
kubectl get nodes -o wide
ssh <node-ip>
sudo tcpdump -i eth0 -w kubernetes-traffic.pcap
```

Transfer the `.pcap` file to your local machine:
```bash
scp <node-ip>:~/kubernetes-traffic.pcap .
```

#### Step 2: Open the File in Wireshark
Analyze the `.pcap` file in Wireshark to inspect:
- Traffic between services.
- Connection issues or anomalies.

---

### 4. Monitoring Metrics with KubeFlow

#### Step 1: Deploy KubeFlow Network Observability
Follow the [KubeFlow deployment guide](https://www.kubeflow.org/docs/components/observability/) to set up observability components.

#### Step 2: Access the Dashboard
Monitor network performance metrics, such as:
- Latency between pods.
- Bandwidth utilization.
- Error rates.

---

### 5. Cleanup

Remove all resources:
```bash
kubectl delete -f network-observability.yaml
kubectl delete -f "https://cloud.weave.works/k8s/scope.yaml"
```

---
