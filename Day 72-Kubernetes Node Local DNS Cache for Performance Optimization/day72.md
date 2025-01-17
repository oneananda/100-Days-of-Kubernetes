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

