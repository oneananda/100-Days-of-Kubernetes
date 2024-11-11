---

## Day 5: Working with ReplicaSets — Ensuring High Availability

### 📘 Overview

A **ReplicaSet** is a Kubernetes object that ensures a specified number of identical Pods are always running in a cluster. It's a key concept for maintaining **high availability** and **fault tolerance**. Understanding ReplicaSets is essential for deploying resilient applications.

---

### What is a ReplicaSet?

- A ReplicaSet helps maintain a **stable set of pod replicas** running at any time.
- It automatically replaces any Pods that fail or are terminated to maintain the desired count.
- ReplicaSets allow applications to be **highly available** and **scalable** by managing multiple instances of Pods.

---
