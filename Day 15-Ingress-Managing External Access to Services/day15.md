---

## Day 15: Ingress — Managing External Access to Services

### 📘 Overview

**Ingress** in Kubernetes allows you to manage external HTTP and HTTPS access to services within your cluster. It provides advanced routing capabilities, such as path-based and host-based routing, while reducing the need for multiple LoadBalancers or NodePort services.

---

### What is an Ingress?

- An **Ingress** is an API object that provides HTTP and HTTPS routing to services in a Kubernetes cluster.
- It acts as an entry point for external traffic.
- Typically used with an **Ingress Controller**, such as NGINX, Traefik, or HAProxy, which implements the Ingress resource.

---

### Key Features of Ingress

1. **Path-Based Routing**: Direct traffic to different services based on URL paths.
2. **Host-Based Routing**: Serve multiple domains from a single IP address.
3. **TLS Termination**: Secure traffic using SSL/TLS certificates.
4. **Centralized Management**: Manage access to multiple services using a single resource.

---


### Hands-On with Ingress

Let’s create and configure Ingress to expose services externally.

---

### 1. Setting Up an Ingress Controller

#### Deploy an NGINX Ingress Controller (Commonly Used):

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

#### Verify the Ingress Controller:
```bash
kubectl get pods -n ingress-nginx
kubectl get services -n ingress-nginx
```

---

### 2. Creating Services for Ingress

#### Create Two Services:

```yaml
# backend-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP
---
# frontend-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP
```

#### Apply the Services:
```bash
kubectl apply -f backend-service.yaml
kubectl apply -f frontend-service.yaml
```

---


### 3. Deploying Applications for Services

#### Deploy Backend and Frontend Pods:

```yaml
# backend-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
        - name: backend
          image: hashicorp/http-echo
          args:
            - "-text=Hello from Backend"
          ports:
            - containerPort: 8080
---
# frontend-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
        - name: frontend
          image: hashicorp/http-echo
          args:
            - "-text=Hello from Frontend"
          ports:
            - containerPort: 8080
```

#### Apply the Deployments:
```bash
kubectl apply -f backend-deployment.yaml
kubectl apply -f frontend-deployment.yaml
```

---


### 4. Configuring Ingress

#### Create an Ingress Resource:

```yaml
# apply-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: example.com
      http:
        paths:
          - path: /backend
            pathType: Prefix
            backend:
              service:
                name: backend-service
                port:
                  number: 80
          - path: /frontend
            pathType: Prefix
            backend:
              service:
                name: frontend-service
                port:
                  number: 80
```

#### Apply the Ingress Resource:
```bash
kubectl apply -f ingress.yaml
```

#### Verify the Ingress:
```bash
kubectl get ingress
```

---

### 5. Testing the Ingress

#### Add a Hostname to Your `/etc/hosts` File:

```plaintext
<EXTERNAL-IP> example.com
```

Replace `<EXTERNAL-IP>` with the external IP of the Ingress Controller.

#### Access Services via Ingress:

- Visit `http://example.com/backend` to access the backend service.
- Visit `http://example.com/frontend` to access the frontend service.

---

