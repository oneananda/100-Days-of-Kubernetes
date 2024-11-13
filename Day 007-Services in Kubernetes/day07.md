---

## Day 7: Services — Exposing Applications Within and Outside the Cluster

### 📘 Overview

**Services** in Kubernetes provide stable endpoints for applications running in Pods, enabling reliable access to them. Since Pods are ephemeral and can be recreated, Services ensure that applications remain accessible even if the underlying Pods change. Services also enable communication between Pods within the cluster and expose applications to the outside world.

---

### What is a Service?

A Service in Kubernetes provides a consistent way to access a set of Pods, abstracting away their IP addresses. Services use **selectors** to group Pods based on labels, allowing traffic to be automatically routed to the right Pods even if they are replaced.

Key types of Services include:
1. **ClusterIP**: Exposes the Service to other Pods within the cluster.
2. **NodePort**: Exposes the Service on a specific port on each node, making it accessible outside the cluster.
3. **LoadBalancer**: Provisions an external load balancer to distribute traffic across Pods (usually available in cloud environments).
4. **ExternalName**: Maps a Service to an external DNS name.

---
