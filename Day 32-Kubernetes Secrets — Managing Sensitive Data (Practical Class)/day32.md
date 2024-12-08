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

