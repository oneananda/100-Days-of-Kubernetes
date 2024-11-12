---

## Day 6: Deployments and Rolling Updates — Managing App Updates

### 📘 Overview

A **Deployment** in Kubernetes is a higher-level resource that manages ReplicaSets and Pods, providing more control over application updates and versioning. Deployments simplify the management of ReplicaSets and enable you to perform **rolling updates**, **rollbacks**, and **scaling** operations seamlessly.

---

### What is a Deployment?

- A Deployment manages one or more **ReplicaSets** to maintain the desired number of replicas for your application.
- It provides **automated updates** to ensure smooth transitions between different application versions.
- **Rollbacks** allow you to revert to a previous version of your application if an update fails.
- Deployments provide a simple way to **scale** applications up or down.

---


