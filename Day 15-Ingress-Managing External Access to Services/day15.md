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