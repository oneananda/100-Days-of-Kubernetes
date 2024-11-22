---

## Day 16: Horizontal Pod Autoscaling — Dynamic Scaling in Kubernetes

### 📘 Overview

**Horizontal Pod Autoscaling (HPA)** automatically adjusts the number of Pods in a deployment or replica set based on observed CPU utilization or other select metrics. HPA is a powerful way to ensure applications remain responsive under load while optimizing resource usage.

---

### Key Concepts

1. **Horizontal Pod Autoscaler (HPA)**: A Kubernetes API resource that automatically scales the number of Pods in a deployment based on CPU, memory, or custom application metrics.
2. **Metrics Server**: A prerequisite for HPA, the **Metrics Server** collects resource metrics (e.g., CPU and memory) from Kubernetes nodes and Pods.

---

### Hands-On with Horizontal Pod Autoscaling

Today, we’ll configure HPA for a simple web application to handle varying levels of incoming traffic.

---

### 1. Deploying the Metrics Server

If the Metrics Server is not installed, you can deploy it as follows:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

#### Verify the Metrics Server:
```bash
kubectl get deployment metrics-server -n kube-system
```

---

### 2. Deploying a Sample Application

Create a deployment using an NGINX container. Create a file named `nginx-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
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
          resources:
            requests:
              cpu: "100m"
              memory: "128Mi"
            limits:
              cpu: "200m"
              memory: "256Mi"
```

#### Apply the Deployment:
```bash
kubectl apply -f nginx-deployment.yaml
```

#### Verify the Deployment:
```bash
kubectl get deployments
```

---

