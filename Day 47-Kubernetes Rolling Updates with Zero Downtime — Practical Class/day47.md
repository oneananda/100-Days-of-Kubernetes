---

## Day 47: Kubernetes Rolling Updates with Zero Downtime — Practical Class

### 📘 Overview

**Rolling Updates** are a Kubernetes deployment strategy that updates pods in a controlled and sequential manner to ensure zero downtime. It replaces pods in small batches and monitors their readiness before proceeding, making it a safer way to deploy changes in production environments.

This session focuses on implementing Rolling Updates in Kubernetes, configuring strategies, and ensuring application availability during the process.

---

### Objectives

1. Understand the concept of Rolling Updates in Kubernetes.
2. Configure a Deployment for Rolling Updates.
3. Monitor the update process to ensure zero downtime.
4. Test rollback in case of issues during updates.

---

### Key Concepts

1. **Rolling Update**:
   - Updates a deployment incrementally while maintaining application availability.
   - Ensures old pods are terminated only after new pods are ready.

2. **Update Strategies**:
   - **MaxUnavailable**: Maximum number of pods that can be unavailable during the update.
   - **MaxSurge**: Maximum number of extra pods created during the update.

3. **Rollback**:
   - Reverts to a previous version if issues occur during the update.

---

### Practical Exercises

---

### 1. Deploying the Initial Version

#### Step 1: Create a Deployment
Define `deployment-v1.yaml` for the initial version:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolling-demo
spec:
  replicas: 3
  selector:
    matchLabels:
      app: rolling-demo
  template:
    metadata:
      labels:
        app: rolling-demo
    spec:
      containers:
      - name: demo-container
        image: nginx:1.19
        ports:
        - containerPort: 80
```

Apply the deployment:
```bash
kubectl apply -f deployment-v1.yaml
```

Verify the deployment:
```bash
kubectl get deployments
kubectl get pods -l app=rolling-demo
```

Expose the deployment with a service:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: rolling-demo-service
spec:
  selector:
    app: rolling-demo
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: LoadBalancer
```

Apply the service:
```bash
kubectl apply -f service.yaml
```

Check the service:
```bash
kubectl get svc rolling-demo-service
```

---

### 2. Updating the Deployment

#### Step 1: Modify the Deployment for a New Version
Update `deployment-v1.yaml` to create `deployment-v2.yaml` with a new image version:
```yaml
spec:
  template:
    spec:
      containers:
      - name: demo-container
        image: nginx:1.21
```

Apply the updated deployment:
```bash
kubectl apply -f deployment-v2.yaml
```

Monitor the Rolling Update process:
```bash
kubectl rollout status deployment rolling-demo
```

---

### 3. Configuring Rolling Update Parameters

#### Step 1: Customize the Update Strategy
Modify the deployment to include Rolling Update parameters:
```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
```

Apply the updated configuration:
```bash
kubectl apply -f deployment-v2.yaml
```

Verify the strategy:
```bash
kubectl describe deployment rolling-demo
```

---

### 4. Testing Rollback

#### Step 1: Trigger a Faulty Update
Update the deployment to use an invalid image:
```yaml
        image: invalid-image:latest
```

Apply the faulty update:
```bash
kubectl apply -f deployment-v2.yaml
```

Monitor the deployment for failures:
```bash
kubectl rollout status deployment rolling-demo
```

#### Step 2: Rollback to the Previous Version
Rollback the deployment:
```bash
kubectl rollout undo deployment rolling-demo
```

Verify the rollback:
```bash
kubectl get pods -l app=rolling-demo
kubectl describe deployment rolling-demo
```

---

### 5. Cleanup

Remove all resources:
```bash
kubectl delete -f deployment-v2.yaml
kubectl delete svc rolling-demo-service
```

---

### Best Practices for Rolling Updates

1. **Monitor Readiness**:
   - Ensure readiness probes are configured to verify pod health during updates.

2. **Progressive Updates**:
   - Use small batch sizes (maxSurge and maxUnavailable) for safer rollouts.

3. **Monitoring**:
   - Use tools like Prometheus and Grafana to monitor application performance during updates.

4. **Rollback Strategy**:
   - Be prepared to rollback quickly in case of issues.

5. **Automated Rollouts**:
   - Use CI/CD tools to automate deployment updates with validation checks.

---

### 📝 Document Your Progress

In your `day47.md` file, record:
- Steps for creating and updating deployments with Rolling Updates.
- Observations during the update process and rollback.
- Challenges faced and their resolutions.
- Examples of how Rolling Updates improve deployment safety.

---

### 🎯 Outcome for Day 47

By the end of Day 47, you should:
1. Understand the purpose and process of Rolling Updates in Kubernetes.
2. Configure deployments for safe and progressive updates.
3. Monitor the update process to ensure zero downtime.
4. Implement rollback strategies for quick recovery.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Rolling Updates](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment)
- [Kubernetes Deployment Strategies](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#deployment-strategies)
- [Rollback in Kubernetes](https://kubernetes.io/docs/tasks/run-application/rollback-update/)

---