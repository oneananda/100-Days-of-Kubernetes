---

## Day 42: Kubernetes ConfigMaps and Secrets — Advanced Usage Practical Class

### 📘 Overview

While **ConfigMaps** and **Secrets** are commonly used for configuration and sensitive data, advanced techniques can help you leverage these features more effectively. This session focuses on advanced use cases, including volume mounts, environment variable overrides, and working with binary data in Secrets.

---


### Objectives

1. Explore advanced usage of ConfigMaps and Secrets in Kubernetes.
2. Use ConfigMaps and Secrets as volumes.
3. Modify pod behavior dynamically using ConfigMaps and Secrets.
4. Work with binary data in Secrets.

---

### Key Concepts

1. **ConfigMaps**:
   - Store non-sensitive data such as configuration files, environment variables, or command-line arguments.

2. **Secrets**:
   - Store sensitive data like API keys, passwords, or certificates in a Base64-encoded format.

3. **Advanced Usage**:
   - Use ConfigMaps and Secrets as files (mounted as volumes).
   - Dynamically update application behavior by reloading configuration changes.

---


### Practical Exercises

---

### 1. Using ConfigMaps and Secrets as Volumes

#### Step 1: Create a ConfigMap
Define a `configmap-advanced.yaml` file:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  app.properties: |
    server.port=8080
    server.name=example-app
```

Apply the ConfigMap:
```bash
kubectl apply -f configmap-advanced.yaml
```

#### Step 2: Create a Secret
Define a `secret-advanced.yaml` file:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
data:
  db-username: YWRtaW4=  # Base64 for 'admin'
  db-password: cGFzc3dvcmQ=  # Base64 for 'password'
```

Apply the Secret:
```bash
kubectl apply -f secret-advanced.yaml
```

#### Step 3: Create a Pod Using Volumes
Define a `pod-config-secret.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: config-secret-demo
spec:
  containers:
  - name: app-container
    image: busybox
    command: ["sh", "-c", "cat /config/app.properties && cat /secrets/db-username && sleep 3600"]
    volumeMounts:
    - name: config-volume
      mountPath: /config
    - name: secret-volume
      mountPath: /secrets
  volumes:
  - name: config-volume
    configMap:
      name: app-config
  - name: secret-volume
    secret:
      secretName: app-secret
```

Apply the Pod:
```bash
kubectl apply -f pod-config-secret.yaml
```

Verify the pod is running:
```bash
kubectl get pods
```

Access the pod and view the mounted data:
```bash
kubectl exec -it config-secret-demo -- sh
cat /config/app.properties
cat /secrets/db-username
```

---

### 2. Updating ConfigMaps and Secrets Dynamically

#### Step 1: Update the ConfigMap
Modify the `app-config` ConfigMap to change `server.name`:
```bash
kubectl edit configmap app-config
```

Change:
```yaml
server.name=updated-example-app
```

Observe if the changes are reflected in the pod (requires pod restart for volume-mounted ConfigMaps).

#### Step 2: Use `kubectl rollout restart` for Changes
Restart the pod to apply changes dynamically:
```bash
kubectl rollout restart pod config-secret-demo
```

---

### 3. Working with Binary Data in Secrets

#### Step 1: Create a Binary Secret
Create a binary secret using a file:
```bash
echo "binary-data" > binary-file.txt
kubectl create secret generic binary-secret --from-file=binary-file.txt
```

Verify the Secret:
```bash
kubectl get secret binary-secret -o yaml
```

#### Step 2: Mount Binary Data in a Pod
Modify the `pod-config-secret.yaml` file to include the binary secret:
```yaml
  volumes:
  - name: binary-volume
    secret:
      secretName: binary-secret
```

Mount it in a container:
```yaml
    volumeMounts:
    - name: binary-volume
      mountPath: /binary
```

Apply the updated configuration and access the binary data:
```bash
kubectl apply -f pod-config-secret.yaml
kubectl exec -it config-secret-demo -- sh
cat /binary/binary-file.txt
```

---

### 4. Cleanup

Remove all resources:
```bash
kubectl delete pod config-secret-demo
kubectl delete configmap app-config
kubectl delete secret app-secret binary-secret
```

---

