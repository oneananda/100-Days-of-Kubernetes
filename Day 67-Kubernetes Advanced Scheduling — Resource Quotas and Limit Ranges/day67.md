---

## Day 67: Kubernetes Advanced Scheduling — Resource Quotas and Limit Ranges

### 📘 Overview

**Resource Quotas** and **Limit Ranges** are Kubernetes features that ensure fair resource allocation among namespaces and prevent individual workloads from consuming excessive resources. This session explores how to manage resources efficiently using these tools.

---

### Objectives

1. Understand the role of resource quotas and limit ranges in Kubernetes.
2. Learn how to configure and enforce resource quotas for namespaces.
3. Use limit ranges to control resource requests and limits for individual pods and containers.

---

### Key Concepts

1. **Resource Quotas**:
   - Enforce resource usage limits (CPU, memory, storage, etc.) at the namespace level.
   - Prevent resource contention and ensure fair usage.

2. **Limit Ranges**:
   - Define default resource requests and limits for pods and containers within a namespace.
   - Prevent over-provisioning or under-provisioning of resources.

3. **Use Cases**:
   - Multi-tenant environments where namespaces represent teams or projects.
   - Enforcing resource usage policies for better cluster efficiency.

---

### Practical Exercises: Resource Quotas and Limit Ranges

---

#### 1. Configuring Resource Quotas

##### Step 1: Create a Namespace
Create a namespace for testing:
```bash
kubectl create namespace quota-demo
```

##### Step 2: Define a Resource Quota
Create a `resource-quota.yaml` file:
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: quota-demo
spec:
  hard:
    pods: "10"
    requests.cpu: "2"
    requests.memory: "1Gi"
    limits.cpu: "4"
    limits.memory: "2Gi"
```

Apply the resource quota:
```bash
kubectl apply -f resource-quota.yaml
```

Verify the resource quota:
```bash
kubectl get resourcequota -n quota-demo
kubectl describe resourcequota compute-quota -n quota-demo
```

---

#### 2. Testing Resource Quotas

##### Step 1: Deploy a Pod within the Limits
Create a `small-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: small-pod
  namespace: quota-demo
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      requests:
        cpu: "200m"
        memory: "128Mi"
      limits:
        cpu: "500m"
        memory: "256Mi"
```

Apply the configuration:
```bash
kubectl apply -f small-pod.yaml
```

Verify that the pod is running:
```bash
kubectl get pods -n quota-demo
```

##### Step 2: Exceed the Quota
Create a `large-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: large-pod
  namespace: quota-demo
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      requests:
        cpu: "2"
        memory: "2Gi"
      limits:
        cpu: "3"
        memory: "3Gi"
```

Apply the configuration:
```bash
kubectl apply -f large-pod.yaml
```

Observe the failure:
```bash
kubectl describe pod large-pod -n quota-demo
```

---

#### 3. Configuring Limit Ranges

##### Step 1: Define a Limit Range
Create a `limit-range.yaml` file:
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: compute-limits
  namespace: quota-demo
spec:
  limits:
  - type: Container
    default:
      cpu: "500m"
      memory: "256Mi"
    defaultRequest:
      cpu: "200m"
      memory: "128Mi"
    max:
      cpu: "1"
      memory: "512Mi"
    min:
      cpu: "100m"
      memory: "64Mi"
```

Apply the limit range:
```bash
kubectl apply -f limit-range.yaml
```

Verify the limit range:
```bash
kubectl get limitrange -n quota-demo
kubectl describe limitrange compute-limits -n quota-demo
```

---

#### 4. Testing Limit Ranges

##### Step 1: Deploy Pods without Resource Specifications
Create a pod without resource specifications:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: default-pod
  namespace: quota-demo
spec:
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f default-pod.yaml
```

Verify that default requests and limits are applied:
```bash
kubectl describe pod default-pod -n quota-demo
```

##### Step 2: Deploy a Pod Exceeding the Limits
Try deploying a pod with resources beyond the specified max:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: exceeding-pod
  namespace: quota-demo
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      requests:
        cpu: "2"
        memory: "2Gi"
```

Apply the configuration and observe the failure:
```bash
kubectl apply -f exceeding-pod.yaml
kubectl describe pod exceeding-pod -n quota-demo
```

---

#### Cleanup

Remove the resources:
```bash
kubectl delete namespace quota-demo
```

---
