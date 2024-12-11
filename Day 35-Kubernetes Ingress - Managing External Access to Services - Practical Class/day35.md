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