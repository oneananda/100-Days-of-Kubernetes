---

## Day 17: Network Policies — Controlling Network Traffic in Kubernetes

### 📘 Overview

**Network Policies** in Kubernetes are used to control the flow of network traffic to and from Pods. By default, all Pods can communicate with each other without restrictions. Network policies allow you to implement fine-grained control over network access between Pods, enhancing security.

---


### Key Concepts

1. **Namespace Scope**: Network policies are applied at the namespace level.
2. **Label Selectors**: Network policies use label selectors to select the target Pods.
3. **Ingress and Egress Rules**: Define allowed sources/destinations for inbound/outbound traffic.

---

### Hands-On with Network Policies

Today, we’ll explore creating and applying network policies to control traffic between Pods within a Kubernetes cluster.

---

### 1. Deploying a Sample Application

Deploy two applications, `app1` and `app2`, to test network communication.

Create `app1.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1
  labels:
    app: app1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
        - name: app1
          image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: app1-service
spec:
  selector:
    app: app1
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

Create `app2.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2
  labels:
    app: app2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app2
  template:
    metadata:
      labels:
        app: app2
    spec:
      containers:
        - name: app2
          image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: app2-service
spec:
  selector:
    app: app2
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

#### Apply the Deployments and Services:
```bash
kubectl apply -f app1.yaml
kubectl apply -f app2.yaml
```

#### Verify the Deployments:
```bash
kubectl get pods
kubectl get services
```

---


### 2. Creating a Network Policy

Create a network policy to allow `app2` to communicate with `app1`, while restricting other access.

Create a file named `network-policy.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app2-to-app1
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: app1
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: app2
```

#### Apply the Network Policy:
```bash
kubectl apply -f network-policy.yaml
```

#### Verify the Network Policy:
```bash
kubectl get networkpolicy
```

---