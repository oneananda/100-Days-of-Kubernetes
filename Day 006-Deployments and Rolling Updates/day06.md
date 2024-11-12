---

## Day 6: Deployments and Rolling Updates — Managing App Updates

### 📘 Overview

A **Deployment** in Kubernetes is a higher-level resource that manages ReplicaSets and Pods, providing more control over application updates and versioning. Deployments simplify the management of ReplicaSets and enable you to perform **rolling updates**, **rollbacks**, and **scaling** operations seamlessly.

---

### What is a Deployment?

- A Deployment manages one or more **ReplicaSets** to maintain the desired number of replicas for your application.
- It provides **automated updates** to ensure smooth transitions between different application versions.
- **Rollbacks** allow you to revert to a previous version of your application if an update fails.
- Deployments provide a simple way to **scale** applications up or down.

---

### Key Features of Deployments

1. **Rolling Updates**: Gradually replace old Pods with new ones to minimize downtime.
2. **Rollbacks**: Quickly revert to a previous version if an update fails.
3. **Declarative Updates**: Manage application updates by changing the desired state in the Deployment specification.
4. **Scaling**: Easily increase or decrease the number of replicas for your application.

---

### Hands-On with Deployments

In this section, we’ll cover creating, updating, rolling back, and scaling Deployments using `kubectl` commands and YAML configurations.

---

### 1. Creating a Deployment

You can create a Deployment using either `kubectl` commands or a YAML file.

#### Example: Create a Deployment with `kubectl`

Create a Deployment named `nginx-deployment` with 3 replicas, using the Nginx image:

```bash
kubectl create deployment nginx-deployment --image=nginx --replicas=3
```

This command creates a Deployment that automatically manages a ReplicaSet with 3 Nginx Pods.

#### Example: Create a Deployment with a YAML File

Here’s a YAML configuration for an Nginx Deployment. Save this as `nginx-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
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
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

Create the Deployment by applying the YAML file:

```bash
kubectl apply -f nginx-deployment.yaml
```

---

### 2. Inspecting and Managing the Deployment

Once created, you can inspect the Deployment and its managed resources.

#### Check the Status of the Deployment
```bash
kubectl get deployments
```

This command lists all Deployments in the current namespace, showing the desired, current, and available replica counts.

#### View Detailed Information
```bash
kubectl describe deployment nginx-deployment
```

This command shows detailed information about the Deployment, including rollout history, conditions, and events.

---

### 3. Performing a Rolling Update

A rolling update allows you to update the image of your application gradually, without downtime.

#### Example: Update the Deployment Image

Update the `nginx-deployment` to use a newer Nginx image (e.g., `nginx:1.16.0`):

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.16.0
```

Kubernetes will initiate a rolling update, gradually replacing the old Pods with new ones running the updated image.

#### Verify the Update

Monitor the status of the update to ensure it completes successfully:

```bash
kubectl rollout status deployment/nginx-deployment
```

This command shows the progress of the rolling update, including when all replicas have been updated.

---
