---

## Day 99: Kubernetes API Gateway with Kong

### 📘 Overview

Kong is a powerful, open-source API gateway that streamlines the management of microservices in a Kubernetes environment. By deploying Kong as an API gateway, you can efficiently route traffic, enforce security policies, and monitor your API ecosystem. This session will guide you through configuring Kong within Kubernetes, managing microservices traffic, and leveraging advanced features such as rate limiting, authentication, and analytics to enhance your API infrastructure.

---


### Objectives

1. **Configure Kong as an API Gateway:**  
   - Deploy Kong in your Kubernetes cluster and set it up as a gateway to manage API traffic.
  
2. **Manage APIs and Microservices Traffic:**  
   - Define routing rules, services, and routes to seamlessly connect your microservices.

3. **Implement Advanced Features:**  
   - Utilize plugins for rate limiting, authentication, and analytics to secure and monitor your API traffic.

---

### Key Concepts

1. **Kong API Gateway:**  
   - A scalable, high-performance API gateway that offers a rich plugin ecosystem to extend its functionality.

2. **API Management:**  
   - The process of defining, deploying, and maintaining APIs, including tasks such as routing, monitoring, and securing microservices traffic.

3. **Advanced Features:**  
   - **Rate Limiting:** Controls the number of requests a client can make within a given time.
   - **Authentication:** Secures APIs using various authentication methods (e.g., key-auth, JWT, OAuth2).
   - **Analytics:** Provides insights into API usage and performance metrics for informed decision-making.

---


### Practical Exercises: Deploying and Configuring Kong

#### 1. Configuring Kong as an API Gateway

##### Step 1: Install Kong Using Helm

Add the Kong Helm repository and install Kong:
```bash
helm repo add kong https://charts.konghq.com
helm repo update
helm install kong kong/kong --namespace kong --create-namespace \
  --set ingressController.installCRDs=false \
  --set proxy.type=LoadBalancer
```

Verify the installation:
```bash
kubectl get pods -n kong -l app.kubernetes.io/name=kong
```

##### Step 2: Expose Kong's Admin API (Optional)
For testing purposes, you might expose the Admin API using a port-forward:
```bash
kubectl port-forward -n kong svc/kong-admin 8001:8001
```

---

#### 2. Managing APIs and Microservices Traffic

##### Step 1: Define a Service and Route

Create a service definition for your backend microservice:
```yaml
apiVersion: configuration.konghq.com/v1
kind: KongPlugin
metadata:
  name: example-service
  namespace: kong
spec:
  service:
    name: my-backend-service
    port: 80
```

Create a route to forward incoming traffic to your service:
```yaml
apiVersion: configuration.konghq.com/v1
kind: KongIngress
metadata:
  name: example-route
  namespace: kong
spec:
  route:
    methods:
      - GET
    paths:
      - /api
```

Apply your configurations:
```bash
kubectl apply -f example-service.yaml
kubectl apply -f example-route.yaml
```

---

#### 3. Advanced Features: Rate Limiting, Authentication, and Analytics

##### Step 1: Enable Rate Limiting Plugin
Apply a KongPlugin to limit the request rate:
```yaml
apiVersion: configuration.konghq.com/v1
kind: KongPlugin
metadata:
  name: rate-limit
  namespace: kong
config:
  minute: 100
  policy: local
plugin: rate-limiting
```
Attach the plugin to a service or route:
```yaml
apiVersion: configuration.konghq.com/v1
kind: KongConsumer
metadata:
  name: demo-consumer
  namespace: kong
```
Then bind the plugin:
```yaml
apiVersion: configuration.konghq.com/v1
kind: KongPlugin
metadata:
  name: demo-rate-limit
  namespace: kong
config:
  minute: 100
  policy: local
plugin: rate-limiting
```

##### Step 2: Configure Authentication Plugin
Enable key-based authentication:
```yaml
apiVersion: configuration.konghq.com/v1
kind: KongPlugin
metadata:
  name: key-auth
  namespace: kong
plugin: key-auth
```
Create a consumer and provision an API key:
```yaml
apiVersion: configuration.konghq.com/v1
kind: KongConsumer
metadata:
  name: my-consumer
  namespace: kong
username: user1
```
Then create a credential for the consumer:
```yaml
apiVersion: configuration.konghq.com/v1
kind: KongCredential
metadata:
  name: my-key
  namespace: kong
consumerRef: my-consumer
type: key-auth
config:
  key: my-secret-api-key
```

##### Step 3: Integrate Analytics
- Use Kong’s built-in logging plugins (e.g., HTTP Log, Syslog) or integrate with external tools like Prometheus for monitoring.
- Example configuration for HTTP logging:
```yaml
apiVersion: configuration.konghq.com/v1
kind: KongPlugin
metadata:
  name: http-logging
  namespace: kong
config:
  http_endpoint: "http://your-logging-endpoint.example.com"
plugin: http-log
```

Apply the plugin:
```bash
kubectl apply -f http-logging.yaml
```

---


### Best Practices for Managing APIs with Kong

1. **Centralize API Management:**  
   - Use Kong as a centralized gateway to manage and secure all microservice APIs in your cluster.

2. **Utilize Plugins Wisely:**  
   - Leverage Kong plugins to enforce policies (rate limiting, authentication) and gather insights (logging, analytics) without impacting service performance.

3. **Monitor and Tune:**  
   - Continuously monitor API usage and performance, and adjust plugin configurations based on real-world traffic patterns.

4. **Secure the Admin API:**  
   - Restrict access to Kong’s Admin API using network policies or other security measures to prevent unauthorized changes.

5. **Automate Configuration Management:**  
   - Version control your Kong configuration files and integrate them with your CI/CD pipelines for consistent deployments.

---


### 📝 Document Your Progress

In your `day99.md` file, include:
- Steps and commands for deploying Kong with Helm.
- Configuration files for services, routes, and plugins.
- Screenshots or logs showing API routing, rate limiting, and authentication in action.
- Observations on the performance and security benefits of using Kong as your API gateway.

---

### 🎯 Outcome for Day 99

By the end of this session, you will:
1. Deploy and configure Kong as an API gateway in your Kubernetes cluster.
2. Manage APIs and direct microservices traffic effectively using Kong routes and services.
3. Enhance API security and reliability with advanced features such as rate limiting, authentication, and integrated analytics.

---

### 🔗 Additional Resources

- [Kong Documentation](https://docs.konghq.com/)
- [Kong Kubernetes Ingress Controller](https://docs.konghq.com/kubernetes-ingress-controller/)
- [Kong Plugin Hub](https://docs.konghq.com/hub/)
- [Helm Charts for Kong](https://github.com/Kong/charts)
- [Kubernetes API Gateway Patterns](https://cloud.google.com/architecture/api-management)

---