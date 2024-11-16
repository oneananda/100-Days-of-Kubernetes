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