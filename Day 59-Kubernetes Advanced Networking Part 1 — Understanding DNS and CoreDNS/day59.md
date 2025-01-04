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
