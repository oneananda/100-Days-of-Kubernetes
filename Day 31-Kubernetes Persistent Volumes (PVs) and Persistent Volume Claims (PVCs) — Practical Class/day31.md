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

### Practical Exercises

---

### 1. Setting Up a Static Persistent Volume

#### Define a PV:
Save the following YAML as `static-pv.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: static-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data
  persistentVolumeReclaimPolicy: Retain
```

#### Apply the PV:
```bash
kubectl apply -f static-pv.yaml
```

#### Verify the PV:
```bash
kubectl get pv
kubectl describe pv static-pv
```

---

### 2. Creating a Persistent Volume Claim

#### Define a PVC:
Save the following YAML as `static-pvc.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: static-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

#### Apply the PVC:
```bash
kubectl apply -f static-pvc.yaml
```

#### Verify the PVC:
```bash
kubectl get pvc
kubectl describe pvc static-pvc
```

---

### 3. Using the PVC in a Pod

#### Create a Pod with Persistent Storage:
Save the following YAML as `pod-with-pvc.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-pvc
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: storage-volume
      mountPath: /usr/share/nginx/html
  volumes:
  - name: storage-volume
    persistentVolumeClaim:
      claimName: static-pvc
```

#### Apply the Pod:
```bash
kubectl apply -f pod-with-pvc.yaml
```

#### Verify the Pod:
```bash
kubectl get pod pod-with-pvc
kubectl describe pod pod-with-pvc
```

#### Test Data Persistence:
1. Access the pod:
   ```bash
   kubectl exec -it pod-with-pvc -- /bin/sh
   ```
2. Add a file to the mounted volume:
   ```bash
   echo "Hello, Persistent Storage!" > /usr/share/nginx/html/index.html
   ```
3. Exit the pod:
   ```bash
   exit
   ```
4. Delete and recreate the pod:
   ```bash
   kubectl delete pod pod-with-pvc
   kubectl apply -f pod-with-pvc.yaml
   ```
5. Verify the data persists:
   ```bash
   kubectl exec -it pod-with-pvc -- cat /usr/share/nginx/html/index.html
   ```

---

### 4. Dynamic Provisioning with StorageClass

#### Define a StorageClass:
Save the following YAML as `storage-class.yaml`:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: dynamic-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```

#### Apply the StorageClass:
```bash
kubectl apply -f storage-class.yaml
```

#### Create a PVC Using the StorageClass:
Save the following YAML as `dynamic-pvc.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: dynamic-pvc
spec:
  storageClassName: dynamic-storage
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

#### Apply the PVC:
```bash
kubectl apply -f dynamic-pvc.yaml
```

#### Verify the PVC and Associated PV:
```bash
kubectl get pvc dynamic-pvc
kubectl get pv
```

---