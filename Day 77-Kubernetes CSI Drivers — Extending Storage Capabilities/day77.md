---

## Day 77: Kubernetes CSI Drivers — Extending Storage Capabilities

### 📘 Overview

The **Container Storage Interface (CSI)** is a standard that allows Kubernetes to integrate with external storage systems through custom storage drivers. CSI enables dynamic volume provisioning, making it easier to use cloud and on-premises storage solutions. This session explores CSI, how to install and configure custom CSI drivers, and how to enable dynamic volume provisioning for external storage systems.

---

### Objectives

1. Understand the Container Storage Interface (CSI) and its role in Kubernetes storage.  
2. Install and configure custom CSI drivers in Kubernetes.  
3. Use CSI drivers for dynamic volume provisioning with external storage systems.  

---

### Key Concepts

1. **Container Storage Interface (CSI):**  
   - A standardized API for container orchestrators to interact with storage systems.  
   - Enables Kubernetes to support third-party storage solutions seamlessly.  

2. **Dynamic Volume Provisioning:**  
   - Automates the creation and deletion of storage volumes as needed by workloads.  

3. **Custom CSI Drivers:**  
   - Allow integration with external storage systems like Amazon EBS, Azure Disk, and NFS.  

4. **StorageClass:**  
   - Defines storage provisioner settings, parameters, and policies for dynamic provisioning.  

---


### Practical Exercises: Deploying and Using CSI Drivers

---

#### 1. Understanding the CSI Architecture

CSI drivers consist of two main components:
- **Controller Plugin:** Manages the lifecycle of storage volumes.  
- **Node Plugin:** Mounts and unmounts storage volumes on worker nodes.  

---

#### 2. Installing a Custom CSI Driver

##### Step 1: Deploy the CSI Driver
For this example, deploy the **Amazon EBS CSI Driver**:
```bash
kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.12"
```

Verify the driver installation:
```bash
kubectl get pods -n kube-system -l app=ebs-csi-controller
kubectl get pods -n kube-system -l app=ebs-csi-node
```

##### Step 2: Create a StorageClass for EBS
Define a `storageclass.yaml`:
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
provisioner: ebs.csi.aws.com
parameters:
  type: gp2
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Apply the StorageClass:
```bash
kubectl apply -f storageclass.yaml
```

List available StorageClasses:
```bash
kubectl get storageclass
```

---

#### 3. Dynamic Volume Provisioning

##### Step 1: Deploy a PVC and Pod
Create a `pvc.yaml`:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-pvc
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: ebs-sc
```

Apply the PVC:
```bash
kubectl apply -f pvc.yaml
```

Verify the PVC status:
```bash
kubectl get pvc
```

Create a `pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ebs-pod
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - mountPath: "/data"
      name: ebs-volume
  volumes:
  - name: ebs-volume
    persistentVolumeClaim:
      claimName: ebs-pvc
```

Apply the pod configuration:
```bash
kubectl apply -f pod.yaml
```

Verify the pod and volume:
```bash
kubectl get pods
kubectl describe pod ebs-pod
```

---

#### 4. Testing and Cleanup

##### Step 1: Test Volume Mount
Exec into the pod and write data:
```bash
kubectl exec -it ebs-pod -- sh
echo "Hello, CSI!" > /data/hello.txt
cat /data/hello.txt
exit
```

**Expected Result:** Data is successfully written to the mounted volume.

##### Step 2: Delete Resources
Remove the pod and PVC:
```bash
kubectl delete -f pod.yaml
kubectl delete -f pvc.yaml
kubectl delete -f storageclass.yaml
kubectl delete -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.12"
```

---