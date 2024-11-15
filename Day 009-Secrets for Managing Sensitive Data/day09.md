---

## Day 9: Secrets for Managing Sensitive Data

### 📘 Overview

**Secrets** in Kubernetes allow you to store and manage sensitive information such as passwords, OAuth tokens, and SSH keys. They are similar to ConfigMaps but designed specifically for sensitive data, ensuring that sensitive information is encoded and managed securely within the cluster.

---

### What is a Secret?

- A **Secret** is an object used to store small amounts of sensitive data, such as a database password or API key.
- Secret data is encoded in **base64**, making it not directly human-readable, though not fully encrypted.
- Secrets can be injected into Pods as **environment variables** or **mounted volumes**.

---

### Key Features of Secrets

1. **Secure Storage**: Sensitive data is stored in base64-encoded format and can be encrypted using external tools.
2. **Access Control**: Kubernetes RBAC (Role-Based Access Control) can restrict who can view or use Secrets.
3. **Flexible Injection**: Secrets can be injected as environment variables or mounted as files in Pods.

---

### Hands-On with Secrets

We’ll explore creating, inspecting, and using Secrets in Kubernetes to securely pass sensitive data to applications.

---

### 1. Creating Secrets

You can create Secrets from the command line or define them in YAML files.

#### Example: Create a Secret from Literal Values

Use `kubectl` to create a Secret with literal key-value pairs:

```bash
kubectl create secret generic my-secret --from-literal=username=admin --from-literal=password=pass123
```

- `generic`: Specifies the type of Secret.
- `my-secret`: The name of the Secret.
- `--from-literal`: Provides the key-value pairs to store in the Secret.

#### Example: Create a Secret from a File

You can store sensitive data in a file and create a Secret from it.

Create a file `username.txt` with the content `admin` and a file `password.txt` with the content `pass123`.

Then, create the Secret:

```bash
kubectl create secret generic my-secret --from-file=username.txt --from-file=password.txt
```

#### Example: Create a Secret from a YAML File

To create a Secret in YAML, you must encode the data in base64 format.

1. Encode the data using a command-line tool:
   ```bash
   echo -n 'admin' | base64
   ```
   Output: `YWRtaW4=`

   ```bash
   echo -n 'pass123' | base64
   ```
   Output: `cGFzczEyMw==`

2. Create a file named `my-secret.yaml` with the following content:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=
  password: cGFzczEyMw==
```

Apply the YAML file:

```bash
kubectl apply -f my-secret.yaml
```

---

### 2. Inspecting Secrets

#### List All Secrets
```bash
kubectl get secrets
```

This shows all Secrets in the current namespace.

#### View Secret Details
```bash
kubectl describe secret my-secret
```

This command shows metadata about the Secret but does not display its data.

#### Decode Secret Data
To view the actual data stored in a Secret, decode the base64 values:

```bash
kubectl get secret my-secret -o jsonpath='{.data.username}' | base64 --decode
```

```bash
kubectl get secret my-secret -o jsonpath='{.data.password}' | base64 --decode
```

---
