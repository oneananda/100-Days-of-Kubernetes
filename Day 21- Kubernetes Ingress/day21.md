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
