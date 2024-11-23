---

## Day 17: Ingress Controllers and Ingress Resources — Managing External Access

### 📘 Overview

**Ingress** in Kubernetes is used to expose HTTP and HTTPS routes from outside the cluster to services within the cluster. It provides a more advanced mechanism for managing external access, offering features like load balancing, SSL termination, and name-based virtual hosting.

---

### What is an Ingress?

- **Ingress Resource**: A set of rules that define how to route external HTTP/S traffic to services inside the cluster.
- **Ingress Controller**: The implementation responsible for fulfilling Ingress rules. Common controllers include **NGINX**, **Traefik**, and **HAProxy**.

---