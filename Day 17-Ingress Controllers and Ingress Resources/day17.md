---

## Day 17: Ingress Controllers and Ingress Resources — Managing External Access

### 📘 Overview

**Ingress** in Kubernetes is used to expose HTTP and HTTPS routes from outside the cluster to services within the cluster. It provides a more advanced mechanism for managing external access, offering features like load balancing, SSL termination, and name-based virtual hosting.

---

### What is an Ingress?

- **Ingress Resource**: A set of rules that define how to route external HTTP/S traffic to services inside the cluster.
- **Ingress Controller**: The implementation responsible for fulfilling Ingress rules. Common controllers include **NGINX**, **Traefik**, and **HAProxy**.

---

### Key Concepts

1. **Ingress Resource**: Defines how HTTP/S requests should be routed to backend services. It supports path-based and subdomain-based routing.
2. **Ingress Controller**: A pod that listens for Ingress resource events and configures itself to manage the routing. Different controllers have their own features and configurations.

---

### Hands-On with Ingress

In today’s session, we’ll configure an **NGINX Ingress Controller** and an Ingress resource to route requests to multiple services.

---

### 1. Deploying NGINX Ingress Controller

First, deploy the NGINX Ingress Controller using the YAML provided by the Kubernetes community:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

#### Verify the Ingress Controller:
```bash
kubectl get pods -n ingress-nginx
```

Wait until all the pods are in the **Running** state.

---

### 2. Deploying Sample Services

Deploy two sample services, each running a simple NGINX server.

Create `service1.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: service1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: service1
  template:
    metadata:
      labels:
        app: service1
    spec:
      containers:
        - name: nginx
          image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: service1
spec:
  selector:
    app: service1
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

Create `service2.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: service2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: service2
  template:
    metadata:
      labels:
        app: service2
    spec:
      containers:
        - name: nginx
          image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: service2
spec:
  selector:
    app: service2
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

#### Apply the Service Configurations:
```bash
kubectl apply -f service1.yaml
kubectl apply -f service2.yaml
```

#### Verify the Services:
```bash
kubectl get services
```

---


### 3. Creating an Ingress Resource

Create an Ingress resource to route requests to either `service1` or `service2` based on the request path. Create a file named `ingress.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - http:
        paths:
          - path: /service1
            pathType: Prefix
            backend:
              service:
                name: service1
                port:
                  number: 80
          - path: /service2
            pathType: Prefix
            backend:
              service:
                name: service2
                port:
                  number: 80
```

#### Apply the Ingress Configuration:
```bash
kubectl apply -f ingress.yaml
```

#### Verify the Ingress:
```bash
kubectl get ingress
```

---

### 4. Testing the Ingress

Retrieve the external IP of the Ingress controller:

```bash
kubectl get svc -n ingress-nginx
```

Once you have the external IP address, you can test the routing:

- Visit `http://<EXTERNAL_IP>/service1` to be routed to `service1`.
- Visit `http://<EXTERNAL_IP>/service2` to be routed to `service2`.

---

### 5. Cleaning Up

Clean up the resources after testing:

```bash
kubectl delete -f ingress.yaml
kubectl delete -f service1.yaml
kubectl delete -f service2.yaml
kubectl delete -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

---


### 📝 Document Your Progress

In your `day17.md` file, record:
- The YAML configurations for services and Ingress resources.
- Observations about how the Ingress resource routes requests.
- Challenges or insights while deploying and testing Ingress.

---

### 🎯 Outcome for Day 17

By the end of Day 17, you should:
1. Understand how Ingress works and how it differs from standard Services.
2. Be able to deploy an NGINX Ingress Controller and configure routing for multiple services.
3. Gain practical experience in using Ingress for managing HTTP traffic in Kubernetes.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
- [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/)

--- 