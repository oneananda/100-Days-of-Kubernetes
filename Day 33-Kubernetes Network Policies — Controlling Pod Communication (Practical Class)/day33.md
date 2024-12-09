---

## Day 33: Kubernetes Network Policies — Controlling Pod Communication (Practical Class)

### 📘 Overview

**Kubernetes Network Policies** allow you to control traffic flow between pods, namespaces, and external resources. By default, Kubernetes permits unrestricted communication between pods. Using Network Policies, you can enforce security and isolate workloads.

This session focuses on creating and testing Network Policies for common use cases.

---

### Objectives

1. Understand the role of Network Policies in Kubernetes.
2. Create and apply Network Policies to restrict or allow pod communication.
3. Test Network Policies using practical examples.
4. Implement best practices for securing pod networks.

---

### Key Concepts

1. **Default Behavior**:
   - Pods can communicate freely unless a Network Policy is applied.

2. **Selectors**:
   - Policies use **podSelector** and **namespaceSelector** to target specific pods or namespaces.

3. **Ingress and Egress Rules**:
   - **Ingress**: Controls incoming traffic to pods.
   - **Egress**: Controls outgoing traffic from pods.

4. **Network Plugins**:
   - Ensure your cluster uses a CNI (Container Network Interface) plugin that supports Network Policies (e.g., Calico, Cilium).

---

