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