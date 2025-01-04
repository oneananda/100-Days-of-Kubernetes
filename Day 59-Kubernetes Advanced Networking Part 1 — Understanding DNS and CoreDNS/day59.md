---

## Day 59: Kubernetes Advanced Networking Part 1 — Understanding DNS and CoreDNS

### 📘 Overview

Networking is a cornerstone of Kubernetes operations, enabling communication within clusters and with external systems. This session focuses on **DNS in Kubernetes** and how **CoreDNS** functions as the cluster's DNS server.

---

### Objectives

1. Understand how DNS works within Kubernetes clusters.
2. Learn the role of CoreDNS in service discovery.
3. Explore troubleshooting techniques for DNS-related issues.

---

### Key Concepts

1. **DNS in Kubernetes**:
   - Facilitates name resolution for services, pods, and external domains.
   - Eliminates the need to rely on IP addresses.

2. **CoreDNS**:
   - A flexible and extensible DNS server integrated into Kubernetes.
   - Supports service discovery via DNS records.

3. **Cluster DNS Configuration**:
   - Every pod is configured with the cluster DNS server (typically CoreDNS).

---

### Practical Exercises: DNS in Kubernetes

---

#### 1. Exploring DNS in Kubernetes

##### Step 1: Inspect CoreDNS Configuration
Check CoreDNS deployment:
```bash
kubectl get pods -n kube-system -l k8s-app=kube-dns
```

Describe the CoreDNS deployment:
```bash
kubectl describe deployment coredns -n kube-system
```

##### Step 2: Verify DNS Resolution
Deploy a test pod with tools like `nslookup`:
```bash
kubectl run dns-test --image=busybox --restart=Never -- sleep 3600
```

Exec into the pod and test DNS resolution:
```bash
kubectl exec -it dns-test -- nslookup kubernetes.default
```

---

#### 2. Service Discovery with CoreDNS

##### Step 1: Deploy a Sample Service
Deploy an Nginx service:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

Apply the configuration:
```bash
kubectl apply -f nginx-service.yaml
```

##### Step 2: Resolve Service Names
Exec into the test pod and resolve the service:
```bash
kubectl exec -it dns-test -- nslookup nginx-service
```

Test FQDN (Fully Qualified Domain Name):
```bash
kubectl exec -it dns-test -- nslookup nginx-service.default.svc.cluster.local
```

---

#### 3. Troubleshooting DNS

##### Step 1: Check CoreDNS Logs
Retrieve CoreDNS logs:
```bash
kubectl logs -l k8s-app=kube-dns -n kube-system
```

Analyze the logs for errors or latency.

##### Step 2: Test External Name Resolution
Verify if external domains can be resolved:
```bash
kubectl exec -it dns-test -- nslookup google.com
```

If resolution fails, check CoreDNS config maps:
```bash
kubectl get configmap coredns -n kube-system -o yaml
```

---

#### Cleanup

Delete the test resources:
```bash
kubectl delete pod dns-test
kubectl delete -f nginx-service.yaml
```

---

### Best Practices for DNS Management in Kubernetes

1. **Monitor CoreDNS Logs**:
   - Regularly check logs for errors or high latency.

2. **Leverage Service Discovery**:
   - Use DNS names instead of hardcoding IPs.

3. **Optimize CoreDNS Configuration**:
   - Configure cache and upstream resolvers for better performance.

4. **Test DNS Resolution**:
   - Periodically validate internal and external DNS queries.

---
