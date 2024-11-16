---

## Day 10: Exploring Volumes — Persistent Storage for Pods

### 📘 Overview

In Kubernetes, **Volumes** provide a way to store and manage data that outlives the lifecycle of a Pod. While containers are ephemeral, Volumes allow data to persist even if the container restarts. Kubernetes supports several types of Volumes to address different storage needs, from simple temporary storage to persistent networked storage.

---

### What is a Volume?

A **Volume** is a directory accessible to containers in a Pod. The lifecycle of a Volume is tied to the Pod, but some Volume types (e.g., Persistent Volumes) allow data to persist beyond the Pod’s lifecycle.

---

### Types of Kubernetes Volumes

1. **EmptyDir**: Temporary storage created when a Pod is assigned to a Node and deleted when the Pod is terminated.
2. **HostPath**: Mounts a file or directory from the host Node’s filesystem into the Pod.
3. **ConfigMap/Secret**: Provides configuration data or sensitive information as files in a Volume.
4. **PersistentVolume (PV)**: Provides storage that persists independently of Pod lifecycles, usually backed by external storage like NFS, AWS EBS, or GCE Persistent Disk.
5. **PersistentVolumeClaim (PVC)**: A request for storage by a Pod, linked to a PersistentVolume.

---