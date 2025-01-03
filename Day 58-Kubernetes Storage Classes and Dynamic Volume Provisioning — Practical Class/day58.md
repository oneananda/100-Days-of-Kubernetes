---

## Day 58: Kubernetes Storage Classes and Dynamic Volume Provisioning — Practical Class

### 📘 Overview

Kubernetes provides **Storage Classes** and **Dynamic Volume Provisioning** to abstract and automate storage management. This session focuses on understanding storage classes, provisioning persistent volumes dynamically, and integrating them with workloads.

---

### Objectives

1. Understand the role of storage classes in Kubernetes.
2. Create and manage dynamic volume provisioning using storage classes.
3. Configure persistent volumes (PVs) and persistent volume claims (PVCs) for applications.
4. Explore advanced storage features like reclaim policies and binding modes.

---

### Key Concepts

1. **Storage Classes**:
   - Define storage parameters for dynamic provisioning.
   - Allow clusters to use different storage backends (e.g., AWS EBS, GCP PD, NFS).

2. **Dynamic Volume Provisioning**:
   - Automatically provisions storage when a PVC is created.

3. **Persistent Volumes (PVs)**:
   - Represent physical or virtual storage in the cluster.

4. **Persistent Volume Claims (PVCs)**:
   - Allow pods to request specific storage dynamically or statically.

5. **Reclaim Policies**:
   - Determine what happens to a PV after the associated PVC is deleted (`Retain`, `Recycle`, `Delete`).
