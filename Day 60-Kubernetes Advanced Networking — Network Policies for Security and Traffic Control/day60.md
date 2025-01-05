---

## Day 60: Kubernetes Advanced Networking — Network Policies for Security and Traffic Control

### 📘 Overview

Network Policies in Kubernetes allow you to control traffic flow at the pod level. This session focuses on creating and applying network policies to secure workloads and manage communication between pods and services.

---

### Objectives

1. Learn how network policies control traffic.
2. Create and test network policies for Kubernetes workloads.
3. Debug and monitor network policy behavior.

---

### Key Concepts

1. **Network Policies**:
   - Define rules for ingress and egress traffic.
   - Allow or restrict traffic between pods or external systems.

2. **Selectors**:
   - Use labels to specify the pods affected by the policy.

3. **Default Deny Policy**:
   - Block all traffic by default and explicitly allow required connections.

---

### Practical Exercises: Network Policies

---

#### 1. Default Deny Policy

##### Step 1: Create a Namespace
Create a new namespace:
```bash
kubectl create namespace netpol-demo
```

##### Step 2: Deploy Pods
Deploy two pods:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-a
  namespace: netpol-demo
  labels:
    app: pod-a
spec:
  containers:
  - name: nginx
    image: nginx
---
apiVersion: v1
kind: Pod
metadata:
  name: pod-b
  namespace: netpol-demo
  labels:
    app: pod-b
spec:
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f pod-a.yaml
kubectl apply -f pod-b.yaml
```

##### Step 3: Apply a Default Deny Policy
Create a `default-deny.yaml` file:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
  namespace: netpol-demo
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

Apply the policy:
```bash
kubectl apply -f default-deny.yaml
```

Verify that traffic is blocked:
```bash
kubectl exec pod-a -n netpol-demo -- curl pod-b.netpol-demo.svc.cluster.local
```

---

#### 2. Allow Specific Traffic

##### Step 1: Define an Allow Policy
Create an `allow-policy.yaml` file:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-pod-a-to-pod-b
  namespace: netpol-demo
spec:
  podSelector:
    matchLabels:
      app: pod-b
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: pod-a
```

Apply the policy:
```bash
kubectl apply -f allow-policy.yaml
```

Verify that traffic from `pod-a` to `pod-b` is allowed:
```bash
kubectl exec pod-a -n netpol-demo -- curl pod-b.netpol-demo.svc.cluster.local
```

---

#### Cleanup

Delete the resources:
```bash
kubectl delete namespace netpol-demo
```

---

### Best Practices for Network Policies

1. **Apply Default Deny Policies**:
   - Block all traffic by default and allow only necessary communication.

2. **Use Labels Effectively**:
   - Clearly label pods to simplify network policy definitions.

3. **Test Policies Regularly**:
   - Verify that policies work as expected using test pods and tools.

4. **Monitor Network Behavior**:
   - Use tools like `kubectl logs` or network plugins to debug connectivity issues.

---

### 📝 Document Your Progress

In your `day60.md` file, include:
- Examples of network policies you implemented.
- Observations on traffic control using policies.
- Challenges faced during testing and their solutions.

---

### 🎯 Outcome for Day 60

By the end of this session, you will:
1. Understand how network policies secure workloads.
2. Implement and test traffic control rules.
3. Debug and refine network policies for robust communication control.

---