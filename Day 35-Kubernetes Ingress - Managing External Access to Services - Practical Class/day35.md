---

## Day 35: Kubernetes Ingress — Managing External Access to Services (Practical Class)

### 📘 Overview

**Kubernetes Ingress** provides a way to manage external access to your services in a cluster. It allows routing HTTP and HTTPS traffic based on defined rules, acting as a layer 7 load balancer.

This practical session focuses on setting up Ingress controllers, defining Ingress rules, and testing traffic routing for multiple services.

---


### Objectives

1. Understand the role of Kubernetes Ingress.
2. Deploy and configure an Ingress controller.
3. Create Ingress rules to route traffic to multiple services.
4. Test HTTPS setup with TLS certificates.

---

### Key Concepts

1. **Ingress Resource**:
   - Defines routing rules for HTTP/HTTPS traffic to services.
   - Supports host- and path-based routing.

2. **Ingress Controller**:
   - Processes Ingress resources and manages routing.
   - Popular options: NGINX Ingress, Traefik, HAProxy, etc.

3. **TLS Support**:
   - Ingress supports secure HTTPS traffic using TLS certificates.

---


### Practical Exercises

---

### 1. Installing an Ingress Controller

#### Install NGINX Ingress Controller:
Use the official Kubernetes NGINX Ingress manifest:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

#### Verify the Installation:
```bash
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

Take note of the external IP for the ingress controller service.

---

### 2. Deploying Sample Services

#### Create a Namespace:
```bash
kubectl create namespace ingress-demo
```

#### Deploy Two Sample Services:
Save the following YAML as `services.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1
  namespace: ingress-demo
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
        image: hashicorp/http-echo
        args:
        - "-text=Welcome to App 1"
        ports:
        - containerPort: 5678
---
apiVersion: v1
kind: Service
metadata:
  name: app1
  namespace: ingress-demo
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5678
  selector:
    app: app1
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2
  namespace: ingress-demo
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
        image: hashicorp/http-echo
        args:
        - "-text=Welcome to App 2"
        ports:
        - containerPort: 5678
---
apiVersion: v1
kind: Service
metadata:
  name: app2
  namespace: ingress-demo
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5678
  selector:
    app: app2
```

#### Apply the Services:
```bash
kubectl apply -f services.yaml
```

---

### 3. Configuring an Ingress Resource

#### Define an Ingress Resource:
Save the following YAML as `ingress.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: ingress-demo
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: demo.local
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: app1
            port:
              number: 80
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: app2
            port:
              number: 80
```

#### Apply the Ingress Resource:
```bash
kubectl apply -f ingress.yaml
```

#### Test the Ingress:
Add `demo.local` to your `/etc/hosts` file, mapping it to the ingress controller's external IP:
```bash
<EXTERNAL_IP> demo.local
```

Access the services in your browser or with `curl`:
```bash
curl http://demo.local/app1
curl http://demo.local/app2
```

---

### 4. Enabling HTTPS with TLS

#### Create a Self-Signed Certificate:
Generate a certificate and key:
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=demo.local/O=demo.local"
```

Create a Kubernetes Secret for TLS:
```bash
kubectl create secret tls demo-tls --key tls.key --cert tls.crt -n ingress-demo
```

#### Update the Ingress Resource:
Save the following as `ingress-tls.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: ingress-demo
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo.local
    secretName: demo-tls
  rules:
  - host: demo.local
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: app1
            port:
              number: 80
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: app2
            port:
              number: 80
```

Apply the updated Ingress:
```bash
kubectl apply -f ingress-tls.yaml
```

Test HTTPS access:
```bash
curl https://demo.local/app1 --insecure
curl https://demo.local/app2 --insecure
```

---


### Best Practices for Ingress

1. **Use Hostnames**:
   - Prefer host-based routing for production setups.

2. **Enable HTTPS**:
   - Always secure traffic with TLS certificates.

3. **Monitor Ingress Traffic**:
   - Use tools like Prometheus and Grafana for monitoring.

4. **External DNS**:
   - Integrate with external DNS services for dynamic domain management.

5. **Ingress Classes**:
   - Specify ingress controllers using annotations to avoid conflicts.

---


### 📝 Document Your Progress

In your `day35.md` file, record:
- YAML configurations for services, Ingress, and TLS.
- Steps for testing HTTP and HTTPS access.
- Observations on traffic routing and challenges faced.

---

### 🎯 Outcome for Day 35

By the end of Day 35, you should:
1. Understand the purpose of Kubernetes Ingress.
2. Deploy and configure an Ingress controller.
3. Create and test Ingress rules for multiple services.
4. Enable HTTPS access using TLS certificates.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
- [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/)
- [Securing Ingress with TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls)

---