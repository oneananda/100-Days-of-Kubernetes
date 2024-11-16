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


### Hands-On with Volumes

We’ll explore different types of Volumes by creating and using them in Pods.

---

### 1. Using `EmptyDir`

An **EmptyDir** Volume is created when the Pod starts and is deleted when the Pod stops. It’s useful for temporary storage shared between containers in a Pod.

#### Example: Pod with `EmptyDir`

Create a file named `emptydir-pod.yaml`: [File Link](https://github.com/oneananda/100-Days-of-Kubernetes/blob/main/Day%20010-Volumes-Persistent%20Storage%20for%20Pods/YAMLs/emptydir-pod.yaml)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: emptydir-pod
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: temp-storage
      mountPath: /usr/share/nginx/html
  volumes:
  - name: temp-storage
    emptyDir: {}
```

Apply the Pod configuration:

```bash
kubectl apply -f emptydir-pod.yaml
```

#### Verify the Volume

1. Access the container:
   ```bash
   kubectl exec -it emptydir-pod -- /bin/sh
   ```
2. Check the contents of `/usr/share/nginx/html`. Any files created here will persist for the duration of the Pod’s lifecycle.

---

### 2. Using `HostPath`

A **HostPath** Volume mounts a directory from the host Node into the Pod. It’s useful for applications requiring direct access to host storage.

#### Example: Pod with `HostPath`

Create a file named `hostpath-pod.yaml`:[File Link](https://github.com/oneananda/100-Days-of-Kubernetes/blob/main/Day%20010-Volumes-Persistent%20Storage%20for%20Pods/YAMLs/hostpath-pod.yaml)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostpath-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "echo Hello Kubernetes > /data/hello.txt && sleep 3600"]
    volumeMounts:
    - name: host-storage
      mountPath: /data
  volumes:
  - name: host-storage
    hostPath:
      path: /tmp/hostpath-demo
      type: DirectoryOrCreate
```

Apply the Pod configuration:

```bash
kubectl apply -f hostpath-pod.yaml
```

#### Verify the Volume

Check the host Node’s `/tmp/hostpath-demo` directory to confirm the data is written there.

---

### 3. Using Persistent Volumes (PV) and Persistent Volume Claims (PVC)

**PersistentVolumes** (PVs) provide a way to use external storage that persists beyond Pod lifecycles. **PersistentVolumeClaims** (PVCs) are requests by Pods for storage resources.

#### Step 1: Create a PersistentVolume

Create a file named `persistent-volume.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-demo
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /tmp/pv-demo
```

Apply the PersistentVolume:

```bash
kubectl apply -f persistent-volume.yaml
```

#### Step 2: Create a PersistentVolumeClaim

Create a file named `persistent-volume-claim.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-demo
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

Apply the PersistentVolumeClaim:

```bash
kubectl apply -f persistent-volume-claim.yaml
```

#### Step 3: Use the PVC in a Pod

Create a file named `pvc-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pvc-pod
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: pvc-storage
      mountPath: /usr/share/nginx/html
  volumes:
  - name: pvc-storage
    persistentVolumeClaim:
      claimName: pvc-demo
```

Apply the Pod configuration:

```bash
kubectl apply -f pvc-pod.yaml
```

#### Verify the PVC and Data Persistence

1. Access the Pod:
   ```bash
   kubectl exec -it pvc-pod -- /bin/sh
   ```
2. Create a file in `/usr/share/nginx/html`:
   ```bash
   echo "Hello from PVC" > /usr/share/nginx/html/index.html
   ```
3. Delete and recreate the Pod. The data in the PersistentVolume will still be available.

---

### 4. Inspecting and Managing Volumes

#### View PersistentVolumes
```bash
kubectl get pv
```

#### View PersistentVolumeClaims
```bash
kubectl get pvc
```

---