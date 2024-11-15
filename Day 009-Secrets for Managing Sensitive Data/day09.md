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

