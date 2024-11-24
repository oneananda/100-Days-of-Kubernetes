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


### 1. Creating a Role

Create a role to allow reading Pods within a namespace. Create a file named `read-pods-role.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

#### Apply the Role:
```bash
kubectl apply -f read-pods-role.yaml
```

#### Verify the Role:
```bash
kubectl get role pod-reader -n default -o yaml
```

---


### 2. Creating a RoleBinding

Bind the `pod-reader` role to a specific user or service account. Create a file named `read-pods-binding.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

#### Apply the RoleBinding:
```bash
kubectl apply -f read-pods-binding.yaml
```

#### Verify the RoleBinding:
```bash
kubectl get rolebinding read-pods-binding -n default -o yaml
```

---