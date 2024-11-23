﻿---

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