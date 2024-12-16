---

## Day 40: Kubernetes Ingress Controller — Practical Class

### 📘 Overview

An **Ingress Controller** is a Kubernetes component that provides HTTP and HTTPS routing to services within a cluster. It allows users to expose services externally and configure routing rules, SSL termination, and more.

This session focuses on deploying and configuring an NGINX Ingress Controller to manage external traffic to Kubernetes services.

---


### Objectives

1. Understand the role of an Ingress Controller in Kubernetes.
2. Deploy an NGINX Ingress Controller in a Kubernetes cluster.
3. Configure Ingress resources to expose services with specific routing rules.
4. Test traffic routing and manage TLS/SSL termination.

---

### Key Concepts

1. **Ingress**:
   - A Kubernetes API object that manages external access to services, typically via HTTP/HTTPS.
   - Supports host-based and path-based routing.

2. **Ingress Controller**:
   - A service running in a cluster that watches for Ingress resources and processes them.

3. **TLS Termination**:
   - Decrypting SSL/TLS traffic at the Ingress Controller before routing to services.

4. **Annotations**:
   - Add custom configurations to Ingress resources, like timeout settings, URL rewrites, or rate limits.

---


### Practical Exercises

---

### 1. Deploying the NGINX Ingress Controller

#### Step 1: Install the NGINX Ingress Controller
Apply the official NGINX Ingress Controller YAML manifest:
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

Verify the deployment:
```bash
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

---

### 2. Deploying Sample Applications

#### Step 1: Create Two Sample Deployments
Deploy two sample applications with corresponding services:

**App 1:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1
spec:
  replicas: 2
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
        image: hashicorp/http-echo:latest
        args:
          - "-text=App1 Response"
        ports:
        - containerPort: 5678
---
apiVersion: v1
kind: Service
metadata:
  name: app1
spec:
  selector:
    app: app1
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5678
```

**App 2:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2
spec:
  replicas: 2
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
        image: hashicorp/http-echo:latest
        args:
          - "-text=App2 Response"
        ports:
        - containerPort: 5678
---
apiVersion: v1
kind: Service
metadata:
  name: app2
spec:
  selector:
    app: app2
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5678
```

Apply the configurations:
```bash
kubectl apply -f app1.yaml
kubectl apply -f app2.yaml
```

---

### 3. Configuring an Ingress Resource

#### Step 1: Create an Ingress Resource
Define an Ingress resource to route traffic based on the hostname:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: sample-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: app1.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app1
            port:
              number: 80
  - host: app2.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app2
            port:
              number: 80
```

Apply the configuration:
```bash
kubectl apply -f ingress.yaml
```

#### Step 2: Update Your Hosts File
Update your local `hosts` file to point to the Ingress Controller's external IP:
```bash
<Ingress-Controller-External-IP> app1.local app2.local
```

Verify access:
- Visit `http://app1.local` to see "App1 Response."
- Visit `http://app2.local` to see "App2 Response."

---

### 4. Enabling TLS/SSL Termination

#### Step 1: Create a TLS Secret
Generate a self-signed certificate and create a Kubernetes secret:
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=app1.local"
kubectl create secret tls app1-tls --cert=tls.crt --key=tls.key
```

#### Step 2: Update the Ingress Resource
Modify the Ingress resource to enable TLS:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: sample-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - app1.local
    secretName: app1-tls
  rules:
  - host: app1.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app1
            port:
              number: 80
```

Apply the configuration:
```bash
kubectl apply -f ingress-tls.yaml
```

Verify access:
- Visit `https://app1.local` to see "App1 Response" over HTTPS.

---

### 5. Cleanup

Remove the deployed resources:
```bash
kubectl delete -f ingress.yaml
kubectl delete -f app1.yaml
kubectl delete -f app2.yaml
kubectl delete -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

---
