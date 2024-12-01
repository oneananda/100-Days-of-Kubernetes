---

## Day 24: Kubernetes Service Accounts — Managing Pod Identities and Access

### 📘 Overview

**Kubernetes Service Accounts** are used to provide an identity to pods running within a cluster. This identity is leveraged to authenticate and authorize pods when they interact with the Kubernetes API or external systems. Today’s session focuses on creating, managing, and using service accounts effectively.

---


---

### What are Service Accounts?

1. **Default Service Account**:
   - Every namespace has a default service account that pods use unless another service account is explicitly specified.

2. **Custom Service Accounts**:
   - Custom service accounts can be created to assign specific permissions or credentials to pods.

3. **Service Account Tokens**:
   - Service accounts are associated with tokens that allow them to authenticate with the Kubernetes API.

---

### Key Concepts

1. **RBAC with Service Accounts**:
   - Pair service accounts with specific Roles or ClusterRoles to control what a pod can access.

2. **Attaching Service Accounts to Pods**:
   - Use the `serviceAccountName` field in the pod spec to attach a service account to a pod.

3. **Workload Isolation**:
   - Assigning different service accounts to different workloads enhances security and isolation.

---


### Hands-On with Service Accounts

In today’s session, we’ll create custom service accounts, attach them to pods, and configure permissions.

---

### 1. Creating a Service Account

#### Create a Service Account:
Save the following YAML as `custom-service-account.yaml`:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: custom-sa
  namespace: default
```

#### Apply the Service Account:
```bash
kubectl apply -f custom-service-account.yaml
```

#### Verify the Service Account:
```bash
kubectl get serviceaccounts
kubectl describe serviceaccount custom-sa
```

---

### 2. Attaching a Service Account to a Pod

#### Create a Pod with a Service Account:
Save the following YAML as `sa-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sa-pod
spec:
  serviceAccountName: custom-sa
  containers:
  - name: demo-container
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
```

#### Apply the Pod:
```bash
kubectl apply -f sa-pod.yaml
```

#### Verify the Service Account Attachment:
```bash
kubectl get pod sa-pod -o yaml
kubectl describe pod sa-pod
```

---

### 3. Granting Permissions to a Service Account

#### Create a Role and RoleBinding:
Save the following YAML as `role-and-binding.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: default
subjects:
- kind: ServiceAccount
  name: custom-sa
  namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

#### Apply the Role and RoleBinding:
```bash
kubectl apply -f role-and-binding.yaml
```

#### Test Permissions:
Use `kubectl` to impersonate the service account and check permissions:

```bash
kubectl auth can-i list pods --as=system:serviceaccount:default:custom-sa
```

---

### 4. Using Service Account Tokens

#### Inspect the Token:
Retrieve the token for the service account:

```bash
kubectl describe secret $(kubectl get secrets | grep custom-sa | awk '{print $1}')
```

Use the token to interact with the Kubernetes API using tools like `curl` or `Postman`.

---

### 📝 Document Your Progress

In your `day24.md` file, record:
- YAML configurations for service accounts, roles, and bindings.
- Steps and observations on attaching service accounts to pods and granting permissions.
- Insights on enhancing workload security using service accounts.

---

### 🎯 Outcome for Day 24

By the end of Day 24, you should:
1. Understand the role of service accounts in Kubernetes.
2. Be able to create and manage custom service accounts.
3. Attach service accounts to pods and control permissions using RBAC.
4. Use service account tokens to interact with the Kubernetes API.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Service Accounts](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)
- [Kubernetes RBAC Guide](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)

---