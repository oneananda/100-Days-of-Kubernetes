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


### 3. Creating a ClusterRole

Create a **ClusterRole** to manage nodes across the cluster. Create a file named `node-manager-clusterrole.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: node-manager
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "list", "watch", "delete"]
```

#### Apply the ClusterRole:
```bash
kubectl apply -f node-manager-clusterrole.yaml
```

#### Verify the ClusterRole:
```bash
kubectl get clusterrole node-manager -o yaml
```

---


### 4. Creating a ClusterRoleBinding

Create a **ClusterRoleBinding** to bind the `node-manager` role to a user. Create a file named `node-manager-binding.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: node-manager-binding
subjects:
- kind: User
  name: john
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: node-manager
  apiGroup: rbac.authorization.k8s.io
```

#### Apply the ClusterRoleBinding:
```bash
kubectl apply -f node-manager-binding.yaml
```

#### Verify the ClusterRoleBinding:
```bash
kubectl get clusterrolebinding node-manager-binding -o yaml
```

---


### 5. Testing Permissions

Test the permissions granted by the roles:

- **As `jane`**: Attempt to list pods in the `default` namespace.
- **As `john`**: Attempt to list or delete nodes.

You can use **Kubernetes impersonation** to test these permissions:

```bash
kubectl auth can-i list pods --namespace=default --as=jane
kubectl auth can-i delete nodes --as=john
```

---


### 📝 Document Your Progress

In your `day18.md` file, record:
- The YAML configurations for Roles, ClusterRoles, RoleBindings, and ClusterRoleBindings.
- Observations regarding how permissions affect user actions.
- Challenges or insights related to managing access and security using RBAC.

---

### 🎯 Outcome for Day 18

By the end of Day 18, you should:
1. Understand the role of RBAC in securing Kubernetes clusters.
2. Be able to create and manage Roles, ClusterRoles, RoleBindings, and ClusterRoleBindings.
3. Know how to test permissions and troubleshoot access issues using Kubernetes impersonation.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Role-Based Access Control](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [Kubernetes RBAC Best Practices](https://kubernetes.io/docs/concepts/security/rbac-good-practices/)

---