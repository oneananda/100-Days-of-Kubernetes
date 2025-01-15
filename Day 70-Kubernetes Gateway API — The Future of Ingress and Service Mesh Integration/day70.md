---

## Day 70: Kubernetes Gateway API — The Future of Ingress and Service Mesh Integration

### 📘 Overview

The **Kubernetes Gateway API** is the next evolution of networking APIs, offering a more flexible, extensible, and expressive alternative to the traditional **Ingress API**. It is designed to unify ingress, egress, and service mesh routing under a common standard. This session introduces the Gateway API, compares it with Ingress, and demonstrates how to integrate it with service meshes like **Istio** and **Linkerd**.

---

### Objectives

1. Understand the fundamentals of the Kubernetes Gateway API.  
2. Compare the Gateway API with the traditional Ingress API.  
3. Learn how to configure **GatewayClasses**, **Gateways**, and **Routes**.  
4. Integrate the Gateway API with service meshes like **Istio** and **Linkerd**.  

---

### Key Concepts

1. **Gateway API**:  
   - A set of CRDs (Custom Resource Definitions) that standardize networking for ingress, egress, and service mesh routing.  

2. **GatewayClass**:  
   - Defines the infrastructure-level configuration and behavior for Gateways. Similar to StorageClass for storage.  

3. **Gateway**:  
   - Represents a network endpoint (like a load balancer) that routes traffic to services.  

4. **Routes (HTTPRoute, TCPRoute, etc.)**:  
   - Define how traffic is routed from the Gateway to Kubernetes services based on protocols and rules.  

5. **Ingress vs. Gateway API**:  
   - Gateway API provides more advanced routing, cross-namespace support, and better extensibility than Ingress.  

---

### Practical Exercises: Deploying and Using the Gateway API

---

#### 1. Installing Gateway API CRDs

##### Step 1: Install Gateway API CRDs
Install the latest version of Gateway API:
```bash
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.0.0/standard-install.yaml
```

Verify the CRDs:
```bash
kubectl get crds | grep gateway
```

**Expected Output:** CRDs like `gatewayclasses.gateway.networking.k8s.io` and `httproutes.gateway.networking.k8s.io`.

---

#### 2. Deploying a GatewayClass and Gateway

##### Step 1: Define a GatewayClass
Create a `gateway-class.yaml`:
```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: example-gateway-class
spec:
  controllerName: example.net/gateway-controller
```

Apply the configuration:
```bash
kubectl apply -f gateway-class.yaml
```

##### Step 2: Create a Gateway
Create a `gateway.yaml`:
```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: example-gateway
  namespace: default
spec:
  gatewayClassName: example-gateway-class
  listeners:
  - name: http
    protocol: HTTP
    port: 80
    allowedRoutes:
      namespaces:
        from: All
```

Apply the configuration:
```bash
kubectl apply -f gateway.yaml
```

Verify the Gateway:
```bash
kubectl get gateways
```

---

#### 3. Configuring Routes

##### Step 1: Define an HTTPRoute
Create a `httproute.yaml`:
```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: example-route
  namespace: default
spec:
  parentRefs:
  - name: example-gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /app
    backendRefs:
    - name: example-service
      port: 80
```

Apply the route:
```bash
kubectl apply -f httproute.yaml
```

##### Step 2: Deploy the Backend Service
Create a simple backend service:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  selector:
    app: example
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example
        image: hashicorp/http-echo
        args:
        - "-text=Hello from Gateway API!"
        ports:
        - containerPort: 8080
```

Apply the deployment:
```bash
kubectl apply -f example-backend.yaml
```

Test the route:
```bash
curl http://<gateway-ip>/app
```

**Expected Output:** `Hello from Gateway API!`

---

#### 4. Integrating Gateway API with Istio

##### Step 1: Install Istio with Gateway API Support
Install Istio with Gateway API support:
```bash
istioctl install --set profile=demo
```

Enable Gateway API controllers:
```bash
kubectl apply -f https://github.com/istio/istio/releases/download/1.16.0/samples/gateway-api/istio-gateway.yaml
```

##### Step 2: Create an Istio GatewayClass
```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: istio-gateway-class
spec:
  controllerName: istio.io/gateway-controller
```

Apply the configuration:
```bash
kubectl apply -f istio-gateway-class.yaml
```

**Result:** Istio now handles the Gateway API configuration for ingress traffic.

---

#### Cleanup

Remove the created resources:
```bash
kubectl delete -f example-backend.yaml
kubectl delete -f httproute.yaml
kubectl delete -f gateway.yaml
kubectl delete -f gateway-class.yaml
```

---

### Best Practices for Gateway API

1. **Adopt Gateway API for Future-Proof Networking:**  
   - Migrate from Ingress to Gateway API for enhanced routing and extensibility.  

2. **Use GatewayClasses for Infrastructure Flexibility:**  
   - Define multiple GatewayClasses to support different types of load balancers or mesh integrations.  

3. **Integrate with Service Meshes:**  
   - Leverage Gateway API with Istio or Linkerd for unified ingress and service mesh traffic management.  

4. **Implement Route-Level Access Controls:**  
   - Define granular routing rules using `HTTPRoute`, `TCPRoute`, and `TLSRoute`.  

---

### 📝 Document Your Progress

In your `day70.md` file, include:  
- GatewayClass, Gateway, and Route configurations.  
- Observations on routing behavior and integration with Istio.  
- Challenges faced and solutions implemented.  

---

### 🎯 Outcome for Day 70

By the end of this session, you will:  
1. Understand the Gateway API and how it improves Kubernetes networking.  
2. Deploy and configure Gateways and Routes.  
3. Integrate Gateway API with service meshes like Istio.  

---

### 🔗 Additional Resources

- [Kubernetes Gateway API Documentation](https://gateway-api.sigs.k8s.io/)  
- [Comparing Ingress and Gateway API](https://gateway-api.sigs.k8s.io/concepts/why-gateway/)  
- [Istio and Gateway API Integration](https://istio.io/latest/docs/tasks/traffic-management/ingress/gateway-api/)  

---