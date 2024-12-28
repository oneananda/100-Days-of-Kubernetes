---

## Day 52: Kubernetes Pod Security Policies (PSP) and Security Contexts — Practical Class

### 📘 Overview

Securing Kubernetes workloads is essential for protecting applications and data. **Pod Security Policies (PSPs)** and **Security Contexts** allow administrators to enforce security guidelines on pods and containers, such as restricting privileges, setting file system permissions, and controlling capabilities.

This session focuses on creating and applying Pod Security Policies and configuring Security Contexts for Kubernetes workloads.

---

### Objectives

1. Understand the purpose of Pod Security Policies and Security Contexts.
2. Create and apply Pod Security Policies to enforce security standards.
3. Configure Security Contexts to manage container privileges.
4. Test workload behavior under different security configurations.

---

### Key Concepts

1. **Pod Security Policies (PSPs)**:
   - Cluster-wide resources to define security constraints on pods.
   - Used to control privileges, host network access, volume usage, and more.

2. **Security Context**:
   - Per-container or per-pod configurations for security-related attributes like UID, GID, and filesystem access.

3. **Key Features**:
   - Restricting privileged access.
   - Forcing read-only file systems.
   - Limiting access to host namespaces.

4. **Deprecation**:
   - PSPs are deprecated in Kubernetes 1.21 and replaced by the Pod Security Admission (PSA) framework.

---

### Practical Exercises

---

### 1. Configuring Pod Security Policies

#### Step 1: Create a Pod Security Policy
Define a `psp-restrictive.yaml` file:
```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restrictive-psp
spec:
  privileged: false
  allowPrivilegeEscalation: false
  requiredDropCapabilities:
  - ALL
  volumes:
  - "configMap"
  - "emptyDir"
  - "secret"
  - "persistentVolumeClaim"
  runAsUser:
    rule: MustRunAsNonRoot
  seLinux:
    rule: RunAsAny
  fsGroup:
    rule: MustRunAs
    ranges:
    - min: 1
      max: 65535
  supplementalGroups:
    rule: MustRunAs
    ranges:
    - min: 1
      max: 65535
```

Apply the PSP:
```bash
kubectl apply -f psp-restrictive.yaml
```

---

#### Step 2: Create a Role and RoleBinding for the PSP
Define a `psp-role.yaml` file:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: psp-user
rules:
- apiGroups:
  - policy
  resources:
  - podsecuritypolicies
  resourceNames:
  - restrictive-psp
  verbs:
  - use
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: psp-user-binding
  namespace: default
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: psp-user
subjects:
- kind: User
  name: default
  apiGroup: rbac.authorization.k8s.io
```

Apply the Role and RoleBinding:
```bash
kubectl apply -f psp-role.yaml
```

---

### 2. Testing Pod Security Policies

#### Step 1: Create a Pod that Violates the PSP
Define a `privileged-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: privileged-pod
spec:
  containers:
  - name: test-container
    image: nginx
    securityContext:
      privileged: true
```

Attempt to apply the pod:
```bash
kubectl apply -f privileged-pod.yaml
```

Verify that the pod is denied due to the restrictive PSP:
```bash
kubectl describe pod privileged-pod
```

---

### 3. Configuring Security Contexts

#### Step 1: Create a Pod with Security Context
Define a `secure-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsNonRoot: true
    fsGroup: 1000
  containers:
  - name: test-container
    image: nginx
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      runAsUser: 1000
```

Apply the secure pod:
```bash
kubectl apply -f secure-pod.yaml
```

Verify the pod:
```bash
kubectl describe pod secure-pod
```

---

### 4. Cleanup

Remove all resources:
```bash
kubectl delete -f secure-pod.yaml
kubectl delete -f privileged-pod.yaml
kubectl delete -f psp-role.yaml
kubectl delete -f psp-restrictive.yaml
```

---

### Best Practices for Pod Security

1. **Use Security Contexts**:
   - Apply least privilege principles for all containers.

2. **Restrict Privileges**:
   - Disable privilege escalation and use non-root users.

3. **Read-Only File Systems**:
   - Use read-only root filesystems wherever possible.

4. **Adopt Pod Security Admission (PSA)**:
   - Transition to PSA as PSPs are deprecated.

5. **Audit and Monitor**:
   - Regularly audit security policies and monitor cluster activity.

---
