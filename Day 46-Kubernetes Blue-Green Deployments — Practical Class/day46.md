---

## Day 46: Kubernetes Blue-Green Deployments — Practical Class

### 📘 Overview

**Blue-Green Deployment** is a release strategy that ensures zero downtime and rollback capabilities by running two environments simultaneously: the "blue" (current version) and "green" (new version). Traffic is switched from blue to green only after successful testing.

This session focuses on implementing Blue-Green Deployments in Kubernetes using Deployments, Services, and namespace isolation.

---

### Objectives

1. Understand the concept and benefits of Blue-Green Deployments.
2. Implement Blue-Green Deployments in Kubernetes.
3. Switch traffic between environments using Services.
4. Test rollback and downtime-free transitions.

---

### Key Concepts

1. **Blue-Green Deployment**:
   - Maintains two parallel environments for the current and new versions.
   - Ensures safe rollouts and instant rollbacks.

2. **Service Switching**:
   - Services are updated to route traffic to the desired environment.
   - No changes are made directly to deployments or pods during the transition.

3. **Namespace Isolation**:
   - Separate namespaces can be used to keep blue and green environments distinct.

---

### Practical Exercises

---

### 1. Setting Up the Blue Environment

#### Step 1: Create the Blue Deployment
Define `blue-deployment.yaml` for the current version:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-blue
  labels:
    app: myapp
    version: blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: blue
  template:
    metadata:
      labels:
        app: myapp
        version: blue
    spec:
      containers:
      - name: myapp
        image: nginx:1.19
        ports:
        - containerPort: 80
```

Apply the Blue Deployment:
```bash
kubectl apply -f blue-deployment.yaml
```

#### Step 2: Expose the Blue Deployment
Create a service for the Blue environment:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
    version: blue
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

Verify the Blue environment:
```bash
kubectl get deployments
kubectl get svc myapp-service
```

---

### 2. Setting Up the Green Environment

#### Step 1: Create the Green Deployment
Define `green-deployment.yaml` for the new version:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-green
  labels:
    app: myapp
    version: green
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: green
  template:
    metadata:
      labels:
        app: myapp
        version: green
    spec:
      containers:
      - name: myapp
        image: nginx:1.21
        ports:
        - containerPort: 80
```

Apply the Green Deployment:
```bash
kubectl apply -f green-deployment.yaml
```

Verify both Blue and Green deployments:
```bash
kubectl get deployments
kubectl get pods -l app=myapp
```

---

### 3. Switching Traffic

#### Step 1: Update the Service Selector
Switch the service to route traffic to the Green environment:
```yaml
spec:
  selector:
    app: myapp
    version: green
```

Apply the updated service:
```bash
kubectl apply -f service.yaml
```

Verify that the service is now routing traffic to Green:
```bash
kubectl describe svc myapp-service
kubectl get pods -l version=green
```

---

### 4. Testing and Rollback

#### Step 1: Test the Green Environment
Access the service using its external IP and verify the application version:
```bash
curl http://<SERVICE_EXTERNAL_IP>
```

#### Step 2: Rollback to the Blue Environment
If issues are detected, update the service selector to route traffic back to Blue:
```yaml
spec:
  selector:
    app: myapp
    version: blue
```

Apply the rollback:
```bash
kubectl apply -f service.yaml
```

Verify that the service is now routing traffic to Blue:
```bash
kubectl describe svc myapp-service
kubectl get pods -l version=blue
```

---

### 5. Cleanup

Remove all resources:
```bash
kubectl delete -f blue-deployment.yaml
kubectl delete -f green-deployment.yaml
kubectl delete svc myapp-service
```

---

### Best Practices for Blue-Green Deployments

1. **Pre-Deployment Testing**:
   - Test the Green environment thoroughly before switching traffic.

2. **Traffic Monitoring**:
   - Use monitoring tools like Prometheus or Grafana to observe application performance.

3. **Automation**:
   - Automate Blue-Green Deployments using CI/CD tools like ArgoCD or Jenkins.

4. **Namespace Isolation**:
   - Use namespaces to separate Blue and Green environments for better resource isolation.

5. **Rollback Strategy**:
   - Ensure quick and reliable rollback procedures.

---

### 📝 Document Your Progress

In your `day46.md` file, record:
- Steps for creating and switching Blue-Green environments.
- Observations during traffic switching and rollback.
- Challenges faced and their resolutions.
- Examples of how Blue-Green Deployments improve reliability.

---
