---

## Day 72: Kubernetes Node Local DNS Cache for Performance Optimization

### 📘 Overview

In large-scale Kubernetes clusters, DNS resolution can become a bottleneck, impacting application performance and stability. **NodeLocal DNSCache** improves DNS performance by running a DNS cache on each node, reducing latency and load on the cluster DNS service. This session covers how to configure and deploy NodeLocal DNSCache and troubleshoot DNS latency issues.

---

### Objectives

1. Understand the role of NodeLocal DNSCache in improving DNS performance.  
2. Configure and deploy NodeLocal DNSCache in Kubernetes clusters.  
3. Identify and troubleshoot DNS latency issues in large-scale Kubernetes deployments.  

---

### Key Concepts

1. **NodeLocal DNSCache:**  
   - A DaemonSet that runs a DNS caching agent on each node.  
   - Caches DNS queries locally to reduce DNS resolution time.  

2. **DNS Performance Optimization:**  
   - Minimizes load on the CoreDNS service by reducing redundant DNS queries.  
   - Reduces network hops for DNS lookups.  

3. **Use Cases:**  
   - Large-scale clusters with high DNS query rates.  
   - Applications sensitive to DNS resolution latency.  

---


### Practical Exercises: Deploying and Using NodeLocal DNSCache

---

#### 1. Deploying NodeLocal DNSCache

##### Step 1: Install NodeLocal DNSCache
Apply the NodeLocal DNSCache manifest provided by Kubernetes:
```bash
kubectl apply -f https://k8s.io/examples/admin/nodelocaldns/nodelocaldns.yaml
```

##### Step 2: Verify the Deployment
Check if the NodeLocal DNSCache pods are running:
```bash
kubectl get pods -n kube-system -l k8s-app=kube-dns-node
```

Check the DaemonSet status:
```bash
kubectl get daemonset nodelocaldns -n kube-system
```

**Expected Result:** A pod should be running on every node.

---

#### 2. Configuring CoreDNS to Use NodeLocal DNSCache

##### Step 1: Edit CoreDNS Configuration
Edit the CoreDNS ConfigMap:
```bash
kubectl edit configmap coredns -n kube-system
```

Modify the `forward` plugin to point to `127.0.0.1:53`:
```bash
forward . 127.0.0.1:53
```

##### Step 2: Restart CoreDNS
Restart CoreDNS to apply the changes:
```bash
kubectl rollout restart deployment coredns -n kube-system
```

Verify the CoreDNS logs:
```bash
kubectl logs -l k8s-app=kube-dns -n kube-system
```

---

#### 3. Testing DNS Performance Improvement

##### Step 1: Deploy a Test Pod
Deploy a busybox pod for DNS testing:
```bash
kubectl run dns-test --image=busybox:1.28 --restart=Never -- sleep 3600
```

##### Step 2: Test DNS Resolution Latency
Exec into the test pod and run DNS queries:
```bash
kubectl exec -it dns-test -- nslookup kubernetes.default
```

Measure the time taken for DNS resolution:
```bash
kubectl exec -it dns-test -- time nslookup kubernetes.default
```

**Expected Result:** DNS resolution time should be significantly lower compared to a cluster without NodeLocal DNSCache.

---

#### 4. Troubleshooting DNS Latency Issues

##### Step 1: Check NodeLocal DNSCache Logs
Inspect logs for DNS errors:
```bash
kubectl logs -l k8s-app=kube-dns-node -n kube-system
```

##### Step 2: Verify DNS Configuration
Confirm that pods are using the local DNS cache (`169.254.20.10` by default):
```bash
kubectl exec dns-test -- cat /etc/resolv.conf
```

##### Step 3: Monitor DNS Metrics
If using Prometheus, check DNS metrics:
```bash
kubectl get --raw /metrics | grep coredns
```

Look for high latency or failed DNS requests.

---

#### Cleanup

Remove the deployed resources:
```bash
kubectl delete pod dns-test
kubectl delete -f https://k8s.io/examples/admin/nodelocaldns/nodelocaldns.yaml
```

---