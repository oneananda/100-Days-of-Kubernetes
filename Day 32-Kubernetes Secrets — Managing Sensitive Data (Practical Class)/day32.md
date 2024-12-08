---

## Day 32: Kubernetes Secrets — Managing Sensitive Data (Practical Class)

### 📘 Overview

**Kubernetes Secrets** allow secure management of sensitive data like passwords, API keys, and certificates. Unlike ConfigMaps, Secrets are stored in a base64-encoded format and can be encrypted at rest in etcd. This practical session focuses on creating, managing, and using Secrets securely in Kubernetes.

---


### Objectives

1. Understand the role of Secrets in Kubernetes.
2. Create and manage Secrets using different methods.
3. Use Secrets in pods as environment variables and volumes.
4. Implement best practices for securing sensitive data.

---

### Key Concepts

1. **Types of Secrets**:
   - **Opaque**: Generic key-value pairs (default type).
   - **kubernetes.io/dockerconfigjson**: Docker registry credentials.
   - **TLS**: Certificates and keys for HTTPS.

2. **Encoding**:
   - Secret values are stored in base64-encoded format, not encrypted by default.

3. **Access Control**:
   - Controlled through Role-Based Access Control (RBAC).

---


### Practical Exercises

---

### 1. Creating Secrets

#### Method 1: Using YAML
Save the following YAML as `secret.yaml`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=  # base64 encoded value of 'admin'
  password: cGFzc3dvcmQ=  # base64 encoded value of 'password'
```

#### Apply the Secret:
```bash
kubectl apply -f secret.yaml
```

#### Verify the Secret:
```bash
kubectl get secrets
kubectl describe secret my-secret
```

#### Decode the Secret:
```bash
kubectl get secret my-secret -o jsonpath='{.data.username}' | base64 --decode
kubectl get secret my-secret -o jsonpath='{.data.password}' | base64 --decode
```

---

#### Method 2: Using `kubectl` Command
Create a Secret directly:
```bash
kubectl create secret generic my-secret-cli --from-literal=username=admin --from-literal=password=password
```

Verify the Secret:
```bash
kubectl get secret my-secret-cli -o yaml
```

---

### 2. Using Secrets in Pods

#### Mount Secret as Environment Variables
Save the following YAML as `secret-env-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: env-container
    image: busybox
    command: ["sh", "-c", "echo The username is $USERNAME and the password is $PASSWORD; sleep 3600"]
    env:
    - name: USERNAME
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: username
    - name: PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: password
```

#### Apply the Pod:
```bash
kubectl apply -f secret-env-pod.yaml
```

#### Check the Logs:
```bash
kubectl logs secret-env-pod
```

---

#### Mount Secret as a Volume
Save the following YAML as `secret-volume-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-volume-pod
spec:
  containers:
  - name: volume-container
    image: busybox
    command: ["sh", "-c", "cat /etc/secrets/username; cat /etc/secrets/password; sleep 3600"]
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secrets
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: my-secret
```

#### Apply the Pod:
```bash
kubectl apply -f secret-volume-pod.yaml
```

#### Verify the Mounted Secrets:
```bash
kubectl exec -it secret-volume-pod -- cat /etc/secrets/username
kubectl exec -it secret-volume-pod -- cat /etc/secrets/password
```

---

### 3. Best Practices for Secrets

1. **Encrypt Secrets at Rest**:
   - Enable encryption for Secrets in etcd by configuring `encryptionConfig` in the Kubernetes API server.

2. **Restrict Access**:
   - Use RBAC to limit access to Secrets only to authorized users or services.

3. **Avoid Secrets in Pod Specs**:
   - Do not hardcode sensitive data in pod specifications. Always reference Secrets.

4. **Use External Secret Management**:
   - Integrate tools like HashiCorp Vault or AWS Secrets Manager for advanced security.

5. **Audit Secret Usage**:
   - Regularly review and audit Secret usage and access logs.

---

