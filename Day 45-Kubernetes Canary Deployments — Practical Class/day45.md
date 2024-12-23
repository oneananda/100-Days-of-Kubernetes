﻿---

## Day 45: Kubernetes Canary Deployments — Practical Class

### 📘 Overview

A **Canary Deployment** is a progressive delivery strategy that allows you to release updates to a subset of users while monitoring the changes for potential issues. This method minimizes risk by gradually increasing traffic to the new version while ensuring stability for the majority of users.

This session focuses on implementing Canary Deployments in Kubernetes using Deployments and Services.

---

### Objectives

1. Understand the concept and benefits of Canary Deployments.
2. Configure Kubernetes Deployments for Canary releases.
3. Gradually shift traffic between versions using services.
4. Monitor the deployment to ensure stability.

---

### Key Concepts

1. **Canary Deployment**:
   - Gradual rollout of a new application version to a small subset of users.
   - Allows issues to be identified before full-scale deployment.

2. **Traffic Splitting**:
   - Directing a percentage of traffic to the new version.
   - Adjusted by scaling replicas or configuring ingress controllers.

3. **Monitoring**:
   - Observing metrics and logs during the rollout to ensure the new version is stable.

---

### Practical Exercises

---

### 1. Deploying the Stable Version

#### Step 1: Create a Stable Deployment
Define `deployment-stable.yaml` for the stable version:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: stable-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: stable
  template:
    metadata:
      labels:
        app: myapp
        version: stable
    spec:
      containers:
      - name: myapp
        image: nginx:1.19
        ports:
        - containerPort: 80
```

Apply the stable deployment:
```bash
kubectl apply -f deployment-stable.yaml
```

#### Step 2: Expose the Stable Deployment
Create a service for the stable app:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
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

Verify the service:
```bash
kubectl get svc myapp-service
```

---

### 2. Deploying the Canary Version

#### Step 1: Create a Canary Deployment
Define `deployment-canary.yaml` for the new version:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: canary-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
      version: canary
  template:
    metadata:
      labels:
        app: myapp
        version: canary
    spec:
      containers:
      - name: myapp
        image: nginx:1.21
        ports:
        - containerPort: 80
```

Apply the Canary deployment:
```bash
kubectl apply -f deployment-canary.yaml
```

Verify the deployments:
```bash
kubectl get deployments
kubectl get pods -l app=myapp
```

---

### 3. Traffic Splitting for Canary Deployment

#### Step 1: Adjust the Service Selector
Edit the service to include both stable and canary versions:
```yaml
spec:
  selector:
    app: myapp
```

This will split traffic between the stable and canary versions based on the number of replicas.

#### Step 2: Gradually Increase Traffic
Scale the canary deployment to increase traffic:
```bash
kubectl scale deployment canary-app --replicas=2
```

Monitor traffic distribution:
```bash
kubectl get pods -l app=myapp -o wide
```

---

### 4. Monitoring the Canary Deployment

#### Step 1: Observe Logs and Metrics
Check logs of the canary pods:
```bash
kubectl logs -l version=canary
```

Use monitoring tools like Prometheus or Grafana to observe traffic and errors.

#### Step 2: Rollback if Needed
If issues are detected, scale the canary deployment to zero:
```bash
kubectl scale deployment canary-app --replicas=0
```

---

### 5. Cleanup

Remove all resources:
```bash
kubectl delete -f deployment-stable.yaml
kubectl delete -f deployment-canary.yaml
kubectl delete svc myapp-service
```

---

### Best Practices for Canary Deployments

1. **Gradual Rollout**:
   - Start with minimal traffic to the canary version and increase gradually.

2. **Monitoring**:
   - Continuously monitor logs, metrics, and user feedback.

3. **Rollback Strategy**:
   - Prepare for quick rollback in case of failures.

4. **Automate**:
   - Use tools like Argo Rollouts or Flagger for automated canary deployments.

---

### 📝 Document Your Progress

In your `day45.md` file, record:
- Steps for creating and testing the Canary Deployment.
- Observations on traffic splitting and behavior.
- Challenges faced and resolutions.
- Examples of real-world use cases for Canary Deployments.

---

### 🎯 Outcome for Day 45

By the end of Day 45, you should:
1. Understand the purpose and benefits of Canary Deployments.
2. Deploy and test a Canary release in Kubernetes.
3. Monitor traffic and performance during a gradual rollout.
4. Implement best practices to ensure a safe and efficient deployment process.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Canary Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#canary-deployments)
- [Argo Rollouts for Advanced Deployment Strategies](https://argoproj.github.io/argo-rollouts/)
- [Progressive Delivery Patterns](https://progressive.delivery/)

---