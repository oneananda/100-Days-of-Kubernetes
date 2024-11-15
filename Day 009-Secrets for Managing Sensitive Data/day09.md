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
