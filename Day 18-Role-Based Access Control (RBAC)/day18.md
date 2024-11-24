---

## Day 18: Role-Based Access Control (RBAC) — Securing Kubernetes

### 📘 Overview

**Role-Based Access Control (RBAC)** is a mechanism for controlling access to resources in Kubernetes. It allows administrators to define who can perform actions on which resources, thereby enhancing cluster security. Today’s session focuses on understanding and implementing RBAC to manage permissions within your cluster.

---


### What is RBAC?

- **RBAC** is a method of regulating access to Kubernetes resources based on the roles of individual users or service accounts.
- It uses **Role**, **ClusterRole**, **RoleBinding**, and **ClusterRoleBinding** resources to define permissions.

---

### Key Concepts

1. **Role** and **ClusterRole**:
   - A **Role** defines permissions within a specific namespace.
   - A **ClusterRole** can define permissions cluster-wide or be used in specific namespaces.
2. **RoleBinding** and **ClusterRoleBinding**:
   - **RoleBinding** associates a Role with a user or service account within a namespace.
   - **ClusterRoleBinding** associates a ClusterRole with a user or service account across the entire cluster.

---

### Hands-On with RBAC

In today’s session, we’ll create **Roles** and **RoleBindings** to manage permissions for specific users and service accounts.

---