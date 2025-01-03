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

---

### Practical Exercises

---

#### 1. Understanding Storage Classes

##### Step 1: List Available Storage Classes
Check the storage classes in your cluster:
```bash
kubectl get storageclass
```

##### Step 2: Inspect a Storage Class
Describe a specific storage class to view its parameters:
```bash
kubectl describe storageclass <storage-class-name>
```

---

#### 2. Creating Dynamic Volumes

##### Step 1: Define a PVC for Dynamic Provisioning
Create a `dynamic-pvc.yaml` file:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: dynamic-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: <storage-class-name>
```

Apply the PVC:
```bash
kubectl apply -f dynamic-pvc.yaml
```

Verify the PVC and the dynamically provisioned PV:
```bash
kubectl get pvc
kubectl get pv
```

##### Step 2: Use the PVC in a Pod
Create a pod that uses the PVC:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pvc-test-pod
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: storage
  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: dynamic-pvc
```

Apply the pod configuration:
```bash
kubectl apply -f pvc-test-pod.yaml
```

Verify the pod and check storage is mounted:
```bash
kubectl exec -it pvc-test-pod -- df -h
```

---

#### 3. Exploring Advanced Features

##### Step 1: Reclaim Policies
Update the reclaim policy of a PV:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: custom-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
```

Create a PVC to bind the PV:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: custom-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: manual
```

Apply the PV and PVC:
```bash
kubectl apply -f custom-pv.yaml
kubectl apply -f custom-pvc.yaml
```

Delete the PVC and observe the PV’s status (`Released` with `Retain` policy).

##### Step 2: Binding Modes
Explore the two binding modes:
- **Immediate**: PVC binds to a PV immediately.
- **WaitForFirstConsumer**: Binding happens only when a pod using the PVC is scheduled.

Update the storage class to `WaitForFirstConsumer`:
```yaml
volumeBindingMode: WaitForFirstConsumer
```

---

#### 4. Testing Storage Persistence

##### Step 1: Write Data to the PVC
Exec into the pod and write data:
```bash
kubectl exec -it pvc-test-pod -- sh -c "echo 'Hello Kubernetes!' > /usr/share/nginx/html/index.html"
```

##### Step 2: Restart the Pod
Delete and recreate the pod:
```bash
kubectl delete pod pvc-test-pod
kubectl apply -f pvc-test-pod.yaml
```

Check if the data persists:
```bash
kubectl exec -it pvc-test-pod -- cat /usr/share/nginx/html/index.html
```

---

#### 5. Cleanup

Remove the created resources:
```bash
kubectl delete -f pvc-test-pod.yaml
kubectl delete -f dynamic-pvc.yaml
kubectl delete pvc custom-pvc
kubectl delete pv custom-pv
```

---

### Best Practices for Storage Management

1. **Use Dynamic Provisioning**:
   - Automate storage management with storage classes.

2. **Set Appropriate Reclaim Policies**:
   - Use `Retain` for critical data and `Delete` for ephemeral data.

3. **Monitor Resource Usage**:
   - Use monitoring tools to track storage usage and trends.

4. **Optimize Access Modes**:
   - Choose the correct access mode (ReadWriteOnce, ReadOnlyMany, ReadWriteMany) based on workload requirements.

5. **Backup Critical Data**:
   - Regularly back up data from persistent volumes.

---
