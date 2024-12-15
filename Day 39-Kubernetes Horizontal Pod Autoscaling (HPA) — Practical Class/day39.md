---

## Day 39: Kubernetes Horizontal Pod Autoscaling (HPA) — Practical Class

### 📘 Overview

Scaling applications in Kubernetes is crucial to handle varying workloads efficiently. **Horizontal Pod Autoscaler (HPA)** automatically adjusts the number of pod replicas in a deployment, replica set, or stateful set based on observed CPU/memory utilization or custom metrics.

This session focuses on setting up and configuring HPA to achieve dynamic scaling of pods.

---


### Objectives

1. Understand the concept and importance of HPA in Kubernetes.
2. Deploy an application with HPA enabled.
3. Configure HPA using resource-based and custom metrics.
4. Test HPA behavior under varying loads.

---

### Key Concepts

1. **Horizontal Pod Autoscaler (HPA)**:
   - Adjusts the number of replicas automatically based on resource utilization.
   - Uses the Kubernetes Metrics Server for real-time metrics.

2. **Metrics Server**:
   - A lightweight component that collects resource metrics from Kubernetes nodes and pods.
   - Provides CPU and memory utilization data to the HPA.

3. **Scaling Thresholds**:
   - The target resource utilization percentage for triggering scaling actions.

---


### Practical Exercises

---

### 1. Setting Up the Metrics Server

#### Step 1: Deploy the Metrics Server
Apply the Metrics Server deployment manifest:
```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Verify that the Metrics Server is running:
```bash
kubectl get pods -n kube-system | grep metrics-server
```

---

### 2. Deploying a Sample Application

#### Step 1: Create a Deployment
Deploy a sample Nginx application:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
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
        image: nginx:latest
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 200m
            memory: 256Mi
```

Apply the configuration:
```bash
kubectl apply -f nginx-deployment.yaml
```

---

### 3. Configuring HPA

#### Step 1: Create an HPA Resource
Create an HPA manifest targeting CPU utilization:
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

Apply the configuration:
```bash
kubectl apply -f nginx-hpa.yaml
```

Check the status of the HPA:
```bash
kubectl get hpa nginx-hpa
```

---

### 4. Testing HPA Behavior

#### Step 1: Generate Load
Use a load generator like `kubectl run` to stress the Nginx application:
```bash
kubectl run -i --tty load-generator --image=busybox --restart=Never -- /bin/sh
```

Run an infinite loop to send requests:
```sh
while true; do wget -q -O- http://<Nginx-Service-IP>; done
```

#### Step 2: Observe HPA Scaling
Monitor the HPA scaling pods based on load:
```bash
kubectl get hpa nginx-hpa
kubectl get pods
```

---

### 5. Cleanup

Remove the deployed resources:
```bash
kubectl delete deployment nginx
kubectl delete hpa nginx-hpa
kubectl delete -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

---


### Best Practices for HPA

1. **Metrics Server**:
   - Ensure the Metrics Server is properly configured and available for real-time metrics.

2. **Resource Requests and Limits**:
   - Define resource requests and limits for containers to avoid scaling issues.

3. **Custom Metrics**:
   - Use custom metrics for advanced scaling scenarios.

4. **Load Testing**:
   - Test HPA behavior under different load conditions to fine-tune scaling parameters.

5. **Monitoring**:
   - Use tools like Prometheus and Grafana to monitor HPA performance and resource utilization.

---


### 📝 Document Your Progress

In your `day39.md` file, record:
- Steps to deploy and configure HPA.
- Observations during load testing and scaling behavior.
- Challenges faced and resolutions.
- Screenshots of HPA scaling actions and metrics.

---

### 🎯 Outcome for Day 39

By the end of Day 39, you should:
1. Understand the purpose and working of HPA in Kubernetes.
2. Deploy and test HPA with a sample application.
3. Analyze HPA scaling decisions based on resource utilization.
4. Apply best practices for implementing HPA in production environments.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: HPA](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [Metrics Server Documentation](https://github.com/kubernetes-sigs/metrics-server)
- [Kubernetes HPA with Custom Metrics](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#scaling-on-custom-metrics)

---