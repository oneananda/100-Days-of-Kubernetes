---

## Day 96: Kubernetes Data Protection with OpenEBS and Rook

### 📘 Overview

Ensuring robust data protection is critical in a Kubernetes environment. This session focuses on two powerful storage solutions: **OpenEBS** for container-attached storage and **Rook** for managing scalable Ceph clusters. You'll learn how to deploy these solutions, implement dynamic provisioning, and manage snapshots to safeguard persistent data, thus enabling resilient stateful applications.

---


### Objectives

1. **Deploy OpenEBS:**  
   - Set up OpenEBS to provide container-attached storage for stateful applications in Kubernetes.

2. **Manage Scalable Storage with Rook:**  
   - Deploy and manage Ceph clusters using Rook to create scalable, resilient storage pools.

3. **Implement Dynamic Provisioning and Snapshot Management:**  
   - Enable automated provisioning of persistent volumes.
   - Configure snapshot mechanisms to protect and restore data as needed.

---

### Key Concepts

1. **OpenEBS:**  
   - A container-attached storage solution designed specifically for Kubernetes.
   - Offers different storage engines (e.g., cStor, Jiva) that can be tailored to various performance and reliability needs.

2. **Rook:**  
   - A Kubernetes operator that simplifies deploying and managing Ceph clusters.
   - Provides scalable, fault-tolerant storage with dynamic provisioning capabilities.

3. **Dynamic Provisioning and Snapshots:**  
   - **Dynamic Provisioning:** Automates the creation of persistent volumes based on StorageClass definitions.
   - **Snapshot Management:** Enables point-in-time backups of persistent volumes to safeguard against data loss and facilitate recovery.

---


### Practical Exercises: Data Protection with OpenEBS and Rook

#### 1. Deploying OpenEBS for Container-Attached Storage

**Step 1: Install OpenEBS Using Helm**

Install OpenEBS in your Kubernetes cluster:
```bash
helm repo add openebs https://openebs.github.io/charts
helm repo update
helm install openebs openebs/openebs --namespace openebs --create-namespace
```
Verify that the OpenEBS pods are running:
```bash
kubectl get pods -n openebs
```

**Step 2: Create a StorageClass for OpenEBS**

Create a `openebs-storageclass.yaml` file:
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: openebs-standard
provisioner: openebs.io/provisioner-iscsi
reclaimPolicy: Delete
volumeBindingMode: Immediate
```
Apply the StorageClass:
```bash
kubectl apply -f openebs-storageclass.yaml
```

---

#### 2. Managing Ceph Clusters with Rook for Scalable Storage

**Step 1: Deploy the Rook Operator and Ceph Cluster**

Create a namespace for Rook and deploy the operator:
```bash
kubectl create namespace rook-ceph
kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/common.yaml -n rook-ceph
kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/operator.yaml -n rook-ceph
```
Deploy a Ceph cluster:
```bash
kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/cluster.yaml -n rook-ceph
```
Verify the Ceph pods:
```bash
kubectl get pods -n rook-ceph
```

**Step 2: Create a StorageClass for Rook Ceph**

Create a file named `rook-ceph-storageclass.yaml`:
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: rook-ceph-block
provisioner: rook-ceph.rbd.csi.ceph.com
parameters:
  clusterID: <your-cluster-id>  # Replace with your actual cluster ID
  pool: replicapool
  imageFormat: "2"
  imageFeatures: layering
  csi.storage.k8s.io/fstype: ext4
reclaimPolicy: Delete
volumeBindingMode: Immediate
```
Apply the StorageClass:
```bash
kubectl apply -f rook-ceph-storageclass.yaml
```

---

#### 3. Dynamic Provisioning and Snapshot Management

**Step 1: Deploy a Sample Application with PVC**

Create a PersistentVolumeClaim (PVC) that uses either the OpenEBS or Rook Ceph StorageClass. For example, create a file named `pvc.yaml`:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: sample-pvc
spec:
  storageClassName: openebs-standard  # Or use "rook-ceph-block" if preferred
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```
Apply the PVC:
```bash
kubectl apply -f pvc.yaml
```
Deploy an application (such as Nginx) that mounts the PVC to validate dynamic provisioning.

**Step 2: Configure Snapshot Management**

Create a VolumeSnapshotClass for your CSI driver. Save the following as `volumesnapshotclass.yaml`:
```yaml
apiVersion: snapshot.storage.k8s.io/v1
kind: VolumeSnapshotClass
metadata:
  name: csi-snapclass
driver: csi.openebs.io  # Adjust driver name as per your environment (e.g., for Rook Ceph, use the appropriate CSI driver)
deletionPolicy: Delete
```
Apply the VolumeSnapshotClass:
```bash
kubectl apply -f volumesnapshotclass.yaml
```

Now, create a VolumeSnapshot by defining a `volumesnapshot.yaml`:
```yaml
apiVersion: snapshot.storage.k8s.io/v1
kind: VolumeSnapshot
metadata:
  name: sample-snapshot
spec:
  volumeSnapshotClassName: csi-snapclass
  source:
    persistentVolumeClaimName: sample-pvc
```
Apply the VolumeSnapshot:
```bash
kubectl apply -f volumesnapshot.yaml
```
Verify that the snapshot is created:
```bash
kubectl get volumesnapshot
```

---
