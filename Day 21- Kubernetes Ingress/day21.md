---

## Day 21: Kubernetes Ingress — Managing External Access to Services

### 📘 Overview

**Kubernetes Ingress** is a resource that manages external HTTP and HTTPS access to services within a cluster. It provides load balancing, SSL termination, and name-based virtual hosting. Today’s session focuses on setting up Ingress to expose services to the outside world effectively.

---


### What is Ingress?

- **Ingress** is an API object that defines routing rules to direct external traffic to internal Kubernetes services.
- It allows features like:
  - **Path-based routing**: Route traffic to different services based on the request path.
  - **Host-based routing**: Route traffic to services based on the domain name.
  - **TLS termination**: Secure the connection using HTTPS.
  - **Load balancing**: Distribute traffic across multiple instances of a service.

---


### Key Concepts

1. **Ingress Controller**:
   - A controller that implements the Ingress API, managing the actual network rules.
   - Examples include NGINX Ingress Controller, Traefik, and HAProxy.

2. **Ingress Resource**:
   - A YAML configuration file defining the routing rules and other details.

3. **Backend Services**:
   - The Kubernetes services to which Ingress routes traffic.

---


### Hands-On with Kubernetes Ingress

In today’s session, we’ll deploy an NGINX Ingress Controller and configure an Ingress resource to expose a sample application.

---

### 1. Installing an Ingress Controller

Use the NGINX Ingress Controller:

#### Deploy Using Helm:
```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install nginx-ingress ingress-nginx/ingress-nginx
```

#### Verify the Installation:
```bash
kubectl get pods -n default
kubectl get services -o wide -n default
```

---

### 2. Deploying a Sample Application

Deploy an application to demonstrate Ingress:

#### Create a Deployment and Service:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: demo-app
  template:
    metadata:
      labels:
        app: demo-app
    spec:
      containers:
      - name: demo-app
        image: hashicorp/http-echo:0.2.3
        args:
        - "-text=Hello Kubernetes!"
        ports:
        - containerPort: 5678
---
apiVersion: v1
kind: Service
metadata:
  name: demo-service
spec:
  selector:
    app: demo-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5678
```

#### Apply the Configuration:
```bash
kubectl apply -f demo-app.yaml
```

#### Verify the Deployment:
```bash
kubectl get pods
kubectl get svc demo-service
```

---

### 3. Creating an Ingress Resource

Define an Ingress resource to route traffic to `demo-service`. Save this in `demo-ingress.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: demo.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: demo-service
            port:
              number: 80
```

#### Apply the Ingress Resource:
```bash
kubectl apply -f demo-ingress.yaml
```

#### Verify the Ingress:
```bash
kubectl get ingress demo-ingress
```

---

### 4. Testing the Ingress

1. **Update Your Hosts File**:
   Map the domain `demo.example.com` to your Ingress Controller’s external IP.

   ```bash
   echo "<INGRESS_CONTROLLER_IP> demo.example.com" | sudo tee -a /etc/hosts
   ```

2. **Access the Application**:
   Open a browser or use `curl` to visit `http://demo.example.com`.

---

### 5. Adding TLS Support

Secure the Ingress with a TLS certificate:

#### Create a Secret for TLS:
```bash
kubectl create secret tls demo-tls --cert=path/to/cert.crt --key=path/to/cert.key
```

#### Update the Ingress Resource:
Add the TLS configuration to `demo-ingress.yaml`:

```yaml
spec:
  tls:
  - hosts:
    - demo.example.com
    secretName: demo-tls
```

#### Apply the Changes:
```bash
kubectl apply -f demo-ingress.yaml
```

---