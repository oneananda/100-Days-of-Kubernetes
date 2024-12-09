---

## Day 33: Kubernetes Network Policies — Controlling Pod Communication (Practical Class)

### 📘 Overview

**Kubernetes Network Policies** allow you to control traffic flow between pods, namespaces, and external resources. By default, Kubernetes permits unrestricted communication between pods. Using Network Policies, you can enforce security and isolate workloads.

This session focuses on creating and testing Network Policies for common use cases.

---

### Objectives

1. Understand the role of Network Policies in Kubernetes.
2. Create and apply Network Policies to restrict or allow pod communication.
3. Test Network Policies using practical examples.
4. Implement best practices for securing pod networks.

---

### Key Concepts

1. **Default Behavior**:
   - Pods can communicate freely unless a Network Policy is applied.

2. **Selectors**:
   - Policies use **podSelector** and **namespaceSelector** to target specific pods or namespaces.

3. **Ingress and Egress Rules**:
   - **Ingress**: Controls incoming traffic to pods.
   - **Egress**: Controls outgoing traffic from pods.

4. **Network Plugins**:
   - Ensure your cluster uses a CNI (Container Network Interface) plugin that supports Network Policies (e.g., Calico, Cilium).

---


### Practical Exercises

---

### 1. Preparing the Environment

#### Deploy a Sample Application:
Save the following YAML as `sample-app.yaml`:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: test-ns
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  namespace: test-ns
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: test-ns
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: busybox
        image: busybox
        command: ["sleep", "3600"]
```

#### Apply the Application:
```bash
kubectl apply -f sample-app.yaml
```

#### Verify the Deployments:
```bash
kubectl get pods -n test-ns
```

---

### 2. Creating a Network Policy

#### Allow Ingress Traffic from Specific Pods:
Save the following YAML as `allow-web-to-backend.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-web-to-backend
  namespace: test-ns
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: web
    ports:
    - protocol: TCP
      port: 80
```

#### Apply the Network Policy:
```bash
kubectl apply -f allow-web-to-backend.yaml
```

#### Verify the Policy:
```bash
kubectl describe networkpolicy allow-web-to-backend -n test-ns
```

---

### 3. Testing the Network Policy

#### Test Communication Before Applying the Policy:
1. Exec into a `web` pod:
   ```bash
   kubectl exec -it <web-pod-name> -n test-ns -- /bin/sh
   ```
2. Test connection to `backend` pod:
   ```bash
   wget -qO- http://<backend-pod-ip>:80
   ```

#### Test Communication After Applying the Policy:
1. Repeat the above steps. Only `web` pods should now be able to communicate with `backend` pods.

---

### 4. Egress Network Policy

#### Restrict Outgoing Traffic to External Resources:
Save the following YAML as `restrict-egress.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-egress
  namespace: test-ns
spec:
  podSelector:
    matchLabels:
      app: web
  policyTypes:
  - Egress
  egress:
  - to:
    - ipBlock:
        cidr: 8.8.8.8/33
    ports:
    - protocol: TCP
      port: 53
```

#### Apply the Policy:
```bash
kubectl apply -f restrict-egress.yaml
```

#### Test Egress Restriction:
1. Exec into a `web` pod:
   ```bash
   kubectl exec -it <web-pod-name> -n test-ns -- /bin/sh
   ```
2. Test DNS resolution (should fail except for 8.8.8.8):
   ```bash
   nslookup google.com
   ```

---

### 5. Cleanup

Remove all resources:
```bash
kubectl delete namespace test-ns
```

---


### Best Practices for Network Policies

1. **Default Deny Policy**:
   - Start with a "default deny" policy to block all traffic and explicitly allow required traffic.

2. **Fine-Grained Access Control**:
   - Use specific selectors and IP blocks to restrict communication to the minimum required.

3. **Audit and Monitor Policies**:
   - Regularly audit policies to ensure they align with security requirements.
   - Monitor traffic to validate policy enforcement.

4. **Test Policies in Staging**:
   - Test policies in a staging environment before applying them in production.

---


### 📝 Document Your Progress

In your `day33.md` file, record:
- YAML configurations for Network Policies and test results.
- Observations on how policies impact pod communication.
- Insights and challenges faced while implementing Network Policies.

---

### 🎯 Outcome for Day 33

By the end of Day 33, you should:
1. Understand how to create and use Kubernetes Network Policies.
2. Restrict and allow pod communication using practical examples.
3. Implement both ingress and egress traffic rules.
4. Apply best practices for securing pod communication in Kubernetes.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
- [Calico Documentation](https://docs.projectcalico.org/network-policy/)
- [Cilium Documentation](https://docs.cilium.io/en/stable/policy/)

---