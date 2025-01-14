---

## Day 70: Kubernetes Pod Security Standards (PSS) and Security Contexts

### 📘 Overview

**Pod Security Standards (PSS)** and **Security Contexts** are vital Kubernetes features designed to strengthen the security posture of workloads running in a cluster. PSS defines security policies for workloads, while Security Contexts control security settings for pods and containers. This session covers how to implement and enforce security best practices using both PSS and Security Contexts.

---

### Objectives

1. Understand the role of Pod Security Standards (PSS) in Kubernetes.  
2. Learn how to configure Security Contexts for pods and containers.  
3. Apply PSS and Security Contexts to secure workloads against vulnerabilities and threats.  

---

### Key Concepts

1. **Pod Security Standards (PSS):**  
   - Built-in security policies that define security levels: `Privileged`, `Baseline`, and `Restricted`.  
   - Enforced through namespace labels using Pod Security Admission (PSA).  

2. **Security Context:**  
   - Configures security settings for pods and containers, such as user permissions, file system access, and Linux capabilities.  

3. **PSS Enforcement Modes:**  
   - **Enforce:** Blocks non-compliant pods.  
   - **Audit:** Logs violations but allows pod creation.  
   - **Warn:** Issues warnings but permits pod creation.  

---

### Practical Exercises: Pod Security Standards and Security Contexts

---

#### 1. Configuring Pod Security Standards (PSS)

##### Step 1: Create a Namespace
```bash
kubectl create namespace pss-demo
```

##### Step 2: Apply the **Restricted** PSS
```bash
kubectl label namespace pss-demo pod-security.kubernetes.io/enforce=restricted
```

Verify the label:
```bash
kubectl get namespace pss-demo --show-labels
```

##### Step 3: Test PSS Enforcement
Create a `privileged-pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: privileged-pod
  namespace: pss-demo
spec:
  containers:
  - name: nginx
    image: nginx
    securityContext:
      privileged: true
```

Apply the pod configuration:
```bash
kubectl apply -f privileged-pod.yaml
```

**Expected Result:** The pod creation should fail due to the **Restricted** policy.

---

#### 2. Configuring Security Contexts

##### Step 1: Deploy a Pod Running as a Non-Root User
Create a `non-root-pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: non-root-pod
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  containers:
  - name: nginx
    image: nginx
    securityContext:
      allowPrivilegeEscalation: false
```

Apply the configuration:
```bash
kubectl apply -f non-root-pod.yaml
```

Verify the user ID:
```bash
kubectl exec -it non-root-pod -- id
```

**Expected Result:** The pod runs as user ID **1000** and group ID **3000**.

---

##### Step 2: Restrict Linux Capabilities
Create a `restricted-capabilities-pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: restricted-capabilities-pod
spec:
  containers:
  - name: nginx
    image: nginx
    securityContext:
      capabilities:
        drop:
        - ALL
      readOnlyRootFilesystem: true
```

Apply the configuration:
```bash
kubectl apply -f restricted-capabilities-pod.yaml
```

**Expected Result:** The pod drops all Linux capabilities and runs with a read-only file system.

---

#### 3. Combining PSS and Security Contexts

##### Step 1: Apply the **Baseline** Policy
```bash
kubectl label namespace pss-demo pod-security.kubernetes.io/enforce=baseline --overwrite
```

##### Step 2: Deploy a Secure Pod
Create a `baseline-secured-pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: baseline-secured-pod
  namespace: pss-demo
spec:
  containers:
  - name: nginx
    image: nginx
    securityContext:
      runAsNonRoot: true
      readOnlyRootFilesystem: true
```

Apply the configuration:
```bash
kubectl apply -f baseline-secured-pod.yaml
```

**Expected Result:** The pod successfully runs under the **Baseline** security policy.

---

#### Cleanup

Remove all created resources:
```bash
kubectl delete namespace pss-demo
kubectl delete pod non-root-pod restricted-capabilities-pod baseline-secured-pod
```

---

### Best Practices for PSS and Security Contexts

1. **Use the Restricted PSS for Production:**  
   - Enforce the **Restricted** policy for sensitive workloads.  

2. **Run Containers as Non-Root:**  
   - Always configure containers to run as non-root users.  

3. **Drop Unnecessary Linux Capabilities:**  
   - Minimize the attack surface by dropping unneeded Linux capabilities.  

4. **Audit Before Enforcing Policies:**  
   - Use the **Audit** mode to detect violations without affecting deployments.  

---
