---

## Day 85: Kubernetes Resource Optimization with Vertical Pod Autoscaler (VPA)

### 📘 Overview

Kubernetes applications require proper resource allocation to run efficiently. **Vertical Pod Autoscaler (VPA)** automatically adjusts CPU and memory requests for pods based on usage patterns, helping optimize resource allocation. This session covers understanding VPA, configuring it to optimize resource usage, and best practices for balancing cost and performance.

---


### Objectives  

1. Understand how **Vertical Pod Autoscaler (VPA)** works and its differences from **Horizontal Pod Autoscaler (HPA)**.  
2. Install and configure VPA for dynamic resource adjustments.  
3. Apply best practices for optimizing cost and performance in Kubernetes.  

---

### Key Concepts  

1. **Vertical vs. Horizontal Autoscaling:**  
   - **Vertical Pod Autoscaler (VPA):** Adjusts the CPU and memory requests of a pod dynamically.  
   - **Horizontal Pod Autoscaler (HPA):** Adjusts the number of pod replicas based on CPU/memory utilization or custom metrics.  

2. **Why Use VPA?**  
   - Prevents resource starvation or over-provisioning.  
   - Helps right-size pods based on actual usage trends.  
   - Improves cost efficiency by optimizing resource requests.  

3. **VPA Components:**  
   - **Recommender:** Suggests CPU/memory resource adjustments based on historical data.  
   - **Updater:** Restarts pods to apply new resource recommendations.  
   - **Admission Controller:** Modifies new pod requests before scheduling based on VPA recommendations.  

---


### Practical Exercises: Configuring Vertical Pod Autoscaler  

---

#### 1. Installing Vertical Pod Autoscaler  

##### Step 1: Deploy VPA in Kubernetes  
Clone the VPA repository and deploy VPA components:  
```bash
git clone https://github.com/kubernetes/autoscaler.git
cd autoscaler/vertical-pod-autoscaler/
kubectl apply -f deploy/vpa.yaml
```

Verify that the VPA components are running:  
```bash
kubectl get pods -n kube-system | grep vpa
```

---

#### 2. Configuring VPA for Resource Optimization  

##### Step 1: Deploy a Sample Application  
Create a simple deployment that will be optimized by VPA:  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cpu-memory-demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
        - name: demo-container
          image: vish/stress
          args:
            - "--cpu"
            - "1"
            - "--vm"
            - "1"
            - "--vm-bytes"
            - "50M"
          resources:
            requests:
              cpu: "200m"
              memory: "256Mi"
            limits:
              cpu: "500m"
              memory: "512Mi"
```

Apply the deployment:  
```bash
kubectl apply -f deployment.yaml
```

##### Step 2: Create a VPA Resource  
Define a `VerticalPodAutoscaler` resource for the deployment:  
```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: vpa-demo
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind: Deployment
    name: cpu-memory-demo
  updatePolicy:
    updateMode: "Auto"
```

Apply the VPA configuration:  
```bash
kubectl apply -f vpa.yaml
```

---

#### 3. Monitoring and Validating VPA Recommendations  

##### Step 1: Check VPA Recommendations  
```bash
kubectl get vpa vpa-demo --output yaml
```

The output will show resource recommendations based on actual usage.

##### Step 2: Simulate Load and Observe Changes  
Increase the CPU/memory load on the application:  
```bash
kubectl exec -it <pod-name> -- stress --cpu 2 --vm 2 --vm-bytes 100M
```

Monitor pod restarts and check updated resource requests:  
```bash
kubectl describe pod <pod-name>
```

---


### Best Practices for Balancing Cost and Performance  

1. **Use VPA in Combination with HPA:**  
   - VPA optimizes individual pod resources, while HPA scales replicas dynamically.  

2. **Set Reasonable Limits:**  
   - Avoid excessive resource allocation to prevent waste and unnecessary costs.  

3. **Enable Only Recommendation Mode in Production:**  
   - Instead of auto-updating pods, use `updateMode: "Off"` to get suggestions without disruptions.  

4. **Monitor Trends with Prometheus/Grafana:**  
   - Track resource usage trends to fine-tune VPA settings.  

5. **Test Before Deploying in Production:**  
   - Observe VPA behavior in staging environments before enabling it for critical workloads.  

---


### 📝 Document Your Progress  

In your `day85.md` file, include:  
- Steps for installing and configuring VPA.  
- Observations on resource optimizations and pod restarts.  
- Insights on balancing cost vs. performance in Kubernetes.  

---

### 🎯 Outcome for Day 85  

By the end of this session, you will:  
1. Install and configure Vertical Pod Autoscaler in a Kubernetes cluster.  
2. Automatically optimize CPU and memory allocation for workloads.  
3. Apply best practices to balance cost efficiency and performance.  

---

### 🔗 Additional Resources  

- [VPA Documentation](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler)  
- [Kubernetes Autoscaling Overview](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)  
- [Monitoring Resource Usage](https://grafana.com/docs/grafana/latest/getting-started/monitor-kubernetes/)  

---