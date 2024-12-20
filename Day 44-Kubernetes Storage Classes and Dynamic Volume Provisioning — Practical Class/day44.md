---

## Day 44: Kubernetes Storage Classes and Dynamic Volume Provisioning — Practical Class

### 📘 Overview

**Storage Classes** in Kubernetes define the characteristics of dynamically provisioned storage, such as performance and reclaim policies. This session explores the creation and use of **Storage Classes** for dynamic volume provisioning, enabling automatic creation of Persistent Volumes (PVs) based on application needs.

---


### Objectives

1. Understand Storage Classes and their role in Kubernetes.
2. Create and configure Storage Classes.
3. Use dynamic provisioning to automatically create Persistent Volumes.
4. Test the behavior of dynamically provisioned volumes.

---

### Key Concepts

1. **Storage Classes**:
   - Abstract definitions of storage properties.
   - Allow for dynamic volume provisioning based on storage provider.

2. **Dynamic Volume Provisioning**:
   - Automatically creates Persistent Volumes when a Persistent Volume Claim is made.
   - Eliminates the need for pre-provisioning storage manually.

3. **Reclaim Policies**:
   - **Retain**: Keeps the storage after the Persistent Volume Claim is deleted.
   - **Delete**: Deletes the storage when the Persistent Volume Claim is deleted.

4. **Provisioners**:
   - Plugins or drivers responsible for interacting with storage backends (e.g., AWS EBS, GCE PD, Azure Disk).

---

### Practical Exercises

---

### 1. Creating a Storage Class

#### Step 1: Define a Storage Class
Create a `storage-class.yaml` file:
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-storage
provisioner: kubernetes.io/aws-ebs  # Replace with your cloud provider or provisioner
parameters:
  type: gp2
  fsType: ext4
reclaimPolicy: Delete
volumeBindingMode: Immediate
```

Apply the Storage Class:
```bash
kubectl apply -f storage-class.yaml
```

Verify the Storage Class:
```bash
kubectl get storageclass
```

---

### 2. Using Dynamic Volume Provisioning

#### Step 1: Create a Persistent Volume Claim
Define a `pvc-dynamic.yaml` file:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-dynamic
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: fast-storage
```

Apply the Persistent Volume Claim:
```bash
kubectl apply -f pvc-dynamic.yaml
```

Verify that a Persistent Volume was dynamically created:
```bash
kubectl get pvc
kubectl get pv
```

---

### 3. Attaching the Persistent Volume to a Pod

#### Step 1: Create a Pod with the Dynamic Volume
Define a `pod-dynamic-volume.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-dynamic-volume
spec:
  containers:
  - name: app
    image: busybox
    command: ["sh", "-c", "echo 'Dynamic Storage Test' > /mnt/data/testfile && sleep 3600"]
    volumeMounts:
    - name: dynamic-storage
      mountPath: /mnt/data
  volumes:
  - name: dynamic-storage
    persistentVolumeClaim:
      claimName: pvc-dynamic
```

Apply the configuration:
```bash
kubectl apply -f pod-dynamic-volume.yaml
```

Verify the pod is running:
```bash
kubectl get pods
```

---

### 4. Testing Dynamic Storage Behavior

#### Step 1: Access the Pod and Test Data Persistence
Access the pod:
```bash
kubectl exec -it pod-dynamic-volume -- sh
```

Check the file:
```bash
cat /mnt/data/testfile
```

#### Step 2: Delete the Pod and PVC
Delete the pod:
```bash
kubectl delete pod pod-dynamic-volume
```

Delete the Persistent Volume Claim:
```bash
kubectl delete pvc pvc-dynamic
```

Verify the Persistent Volume based on the reclaim policy:
```bash
kubectl get pv
```

---

### 5. Cleanup

Remove all resources:
```bash
kubectl delete -f pod-dynamic-volume.yaml
kubectl delete -f pvc-dynamic.yaml
kubectl delete -f storage-class.yaml
```

---


### Best Practices for Storage Classes

1. **Reclaim Policies**:
   - Use `Retain` for critical data that should not be deleted automatically.
   - Use `Delete` for temporary or non-critical data.

2. **Storage Class Parameters**:
   - Customize parameters like `fsType` and `type` based on storage requirements.

3. **Default Storage Class**:
   - Mark a Storage Class as default for workloads without a specified Storage Class.

4. **Monitoring**:
   - Use monitoring tools to track storage usage and performance.

---


### 📝 Document Your Progress

In your `day44.md` file, record:
- Steps for creating and using a Storage Class.
- Observations on dynamic provisioning behavior.
- Challenges faced and their resolutions.
- Examples of how Storage Classes simplify storage management in Kubernetes.

---

### 🎯 Outcome for Day 44

By the end of Day 44, you should:
1. Understand the role and functionality of Storage Classes in Kubernetes.
2. Create and use Storage Classes for dynamic volume provisioning.
3. Attach dynamically provisioned volumes to pods.
4. Apply best practices for managing storage in Kubernetes.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)
- [Dynamic Volume Provisioning in Kubernetes](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)
- [Provisioner Parameters](https://kubernetes.io/docs/concepts/storage/storage-classes/#provisioner-parameters)

---