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

