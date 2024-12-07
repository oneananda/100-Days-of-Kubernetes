---

## Day 31: Kubernetes Persistent Volumes (PVs) and Persistent Volume Claims (PVCs) — Practical Class

### 📘 Overview

**Persistent Volumes (PVs)** and **Persistent Volume Claims (PVCs)** enable storage management in Kubernetes, decoupling storage from the pod lifecycle. This practical class focuses on setting up PVs, claiming them with PVCs, and using them in pods.

---


### Objectives

1. Understand the concept of PVs and PVCs in Kubernetes.
2. Configure storage using PVs and PVCs.
3. Mount persistent storage in pods.
4. Practice creating and managing storage resources dynamically and statically.

---

### Key Concepts

1. **Persistent Volume (PV)**:
   - Represents physical storage in a cluster.
   - Created by cluster admins (static provisioning) or dynamically by StorageClasses.

2. **Persistent Volume Claim (PVC)**:
   - A request for storage by a user.
   - Binds to a suitable PV matching the requested size and access mode.

3. **Access Modes**:
   - **ReadWriteOnce (RWO)**: Mounted as read-write by a single node.
   - **ReadOnlyMany (ROX)**: Mounted as read-only by multiple nodes.
   - **ReadWriteMany (RWX)**: Mounted as read-write by multiple nodes.

4. **Reclaim Policy**:
   - **Retain**: Keeps data after PV is released.
   - **Recycle**: Clears data (basic scrub).
   - **Delete**: Deletes the PV and the associated storage.

---


