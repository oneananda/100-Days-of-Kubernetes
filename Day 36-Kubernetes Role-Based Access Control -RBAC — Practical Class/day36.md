---

## Day 36: Kubernetes Role-Based Access Control (RBAC) — Practical Class

### 📘 Overview

**Role-Based Access Control (RBAC)** in Kubernetes allows you to define and enforce permissions for users, groups, and applications. It is essential for securing the cluster by granting only the necessary permissions to resources.

This practical session focuses on creating roles, role bindings, and testing access controls for different users and service accounts.

---


### Objectives

1. Understand the purpose of RBAC in Kubernetes.
2. Create roles and role bindings for fine-grained access control.
3. Test RBAC rules using `kubectl`.
4. Learn best practices for implementing RBAC in production.

---

### Key Concepts

1. **Roles and ClusterRoles**:
   - **Role**: Grants permissions within a specific namespace.
   - **ClusterRole**: Grants permissions cluster-wide or across namespaces.

2. **RoleBinding and ClusterRoleBinding**:
   - **RoleBinding**: Associates a Role with a user or group within a namespace.
   - **ClusterRoleBinding**: Associates a ClusterRole with a user or group across the cluster.

3. **Subjects**:
   - Users, groups, or service accounts that the Role/ClusterRole applies to.

4. **Principle of Least Privilege**:
   - Grant the minimum permissions necessary for a task.

---

### Practical Exercises

---

### 1. Creating a Namespace for Testing

Create a dedicated namespace:
```bash
kubectl create namespace rbac-demo
```

---

### 2. Creating a Role with Limited Permissions

#### Define a Role:
Save the following YAML as `role.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: rbac-demo
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

#### Apply the Role:
```bash
kubectl apply -f role.yaml
```

---

### 3. Creating a Service Account and RoleBinding

#### Create a Service Account:
```bash
kubectl create serviceaccount demo-sa -n rbac-demo
```

#### Bind the Role to the Service Account:
Save the following YAML as `rolebinding.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: rbac-demo
subjects:
- kind: ServiceAccount
  name: demo-sa
  namespace: rbac-demo
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

#### Apply the RoleBinding:
```bash
kubectl apply -f rolebinding.yaml
```

---

### 4. Testing RBAC Permissions

#### Switch to the Service Account:
1. Get the token for the service account:
   ```bash
   kubectl -n rbac-demo get secret $(kubectl -n rbac-demo get sa demo-sa -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode
   ```

2. Configure `kubectl` to use the service account:
   - Save the token and create a kubeconfig file:
     ```bash
     kubectl config set-credentials demo-sa --token=<TOKEN>
     kubectl config set-context demo-sa-context --cluster=<CLUSTER_NAME> --namespace=rbac-demo --user=demo-sa
     kubectl config use-context demo-sa-context
     ```

3. Test access:
   - Try listing pods (should succeed):
     ```bash
     kubectl get pods
     ```
   - Try listing services (should fail):
     ```bash
     kubectl get services
     ```

---

### 5. Creating a ClusterRole and ClusterRoleBinding

#### Define a ClusterRole:
Save the following YAML as `clusterrole.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: namespace-reader
rules:
- apiGroups: [""]
  resources: ["namespaces"]
  verbs: ["get", "list", "watch"]
```

#### Apply the ClusterRole:
```bash
kubectl apply -f clusterrole.yaml
```

#### Bind the ClusterRole to the Service Account:
Save the following YAML as `clusterrolebinding.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: namespace-reader-binding
subjects:
- kind: ServiceAccount
  name: demo-sa
  namespace: rbac-demo
roleRef:
  kind: ClusterRole
  name: namespace-reader
  apiGroup: rbac.authorization.k8s.io
```

#### Apply the ClusterRoleBinding:
```bash
kubectl apply -f clusterrolebinding.yaml
```

#### Test Cluster-Wide Permissions:
Switch to the `demo-sa` context and verify:
```bash
kubectl get namespaces
kubectl get pods --all-namespaces  # Should still fail
```

---

### 6. Cleanup

Remove all resources:
```bash
kubectl delete namespace rbac-demo
kubectl delete clusterrole namespace-reader
kubectl delete clusterrolebinding namespace-reader-binding
```

---