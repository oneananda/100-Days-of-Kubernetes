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

Create a deployment using an NGINX container. Create a file named `hpa-nginx-deployment.yaml`:

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

### 3. Configuring Horizontal Pod Autoscaling

Create an HPA resource that scales based on CPU utilization. Create a file named `nginx-hpa.yaml`:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 1
  maxReplicas: 5
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```

#### Apply the HPA Configuration:
```bash
kubectl apply -f nginx-hpa.yaml
```

#### Verify the HPA:
```bash
kubectl get hpa
```

---

### 4. Stress Testing the Application

To trigger scaling, generate CPU load. Use a load generator or run a stress test:

#### Deploy a Stress Pod:
```bash
kubectl run -i --tty load-generator --image=busybox /bin/sh
```

In the shell, use a loop to generate continuous requests to the NGINX service:

```sh
while true; do wget -q -O- http://nginx-deployment; done
```

#### Monitor Scaling:
```bash
kubectl get hpa -w
```

Observe the number of Pods increasing as the CPU utilization rises above the target.

---

### 5. Cleaning Up

After testing, clean up the resources:

```bash
kubectl delete -f nginx-hpa.yaml
kubectl delete -f hpa-nginx-deployment.yaml
kubectl delete pod load-generator
```

---

### 📝 Document Your Progress

In your `day16.md` file, record:
- YAML configurations for HPA and the NGINX deployment.
- Observations on how the HPA adjusted the number of Pods in response to load.
- Challenges or insights encountered while configuring autoscaling.

---

### 🎯 Outcome for Day 16

By the end of Day 16, you should:
1. Understand the basics of Horizontal Pod Autoscaling and its role in maintaining application performance.
2. Be able to deploy an HPA and observe scaling behavior in response to workload changes.
3. Gain experience in stress testing a Kubernetes application to see how HPA reacts.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [Metrics Server Overview](https://github.com/kubernetes-sigs/metrics-server)

--- 
