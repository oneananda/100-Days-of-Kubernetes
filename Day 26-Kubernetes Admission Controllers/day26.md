---

## Day 26: Kubernetes Admission Controllers — Validating and Mutating Workloads

### 📘 Overview

**Kubernetes Admission Controllers** are plugins that govern and enforce policies on Kubernetes objects during create, update, delete, or connect operations. These controllers ensure that only valid and compliant resources are admitted to the cluster. Today’s session explores how to configure and use admission controllers for cluster security and resource governance.

---


### What are Admission Controllers?

1. **Purpose**:
   - Evaluate requests to the Kubernetes API server before they are persisted in etcd.
   - Modify or validate requests as per defined policies.

2. **Types of Admission Controllers**:
   - **Validating Admission Controllers**:
     - Validate requests and reject those that violate cluster policies.
   - **Mutating Admission Controllers**:
     - Modify requests to enforce default configurations or add metadata.

3. **Examples**:
   - Enforcing pod security policies.
   - Automatically injecting sidecars (e.g., Envoy or Istio sidecars).

---

### Key Concepts

1. **Webhook Admission Controllers**:
   - External webhooks allow custom validation and mutation logic.
   - Example: Injecting environment-specific configurations into pod definitions.

2. **Built-In Admission Controllers**:
   - Kubernetes ships with several controllers like `PodSecurity`, `NamespaceLifecycle`, and `ResourceQuota`.

3. **Order of Execution**:
   - Mutating controllers run first, followed by validating controllers.

---

### Hands-On with Admission Controllers

---

### 1. Enabling Admission Controllers

Admission controllers can be enabled by modifying the API server flags. For example:

```bash
--enable-admission-plugins=NamespaceLifecycle,LimitRanger,MutatingAdmissionWebhook,ValidatingAdmissionWebhook
```

Verify enabled admission controllers:

```bash
kubectl api-versions
```

---

### 2. Setting Up a Mutating Admission Webhook

#### Create a Mutating Webhook Configuration:
Save the following YAML as `mutating-webhook.yaml`:

```yaml
apiVersion: admissionregistration.k8s.io/v1
kind: MutatingWebhookConfiguration
metadata:
  name: example-mutating-webhook
webhooks:
  - name: mutate.example.com
    clientConfig:
      service:
        name: mutating-webhook-service
        namespace: default
        path: /mutate
      caBundle: <BASE64_ENCODED_CA_CERT>
    rules:
      - operations: ["CREATE"]
        apiGroups: [""]
        apiVersions: ["v1"]
        resources: ["pods"]
    admissionReviewVersions: ["v1", "v1beta1"]
    sideEffects: None
```

#### Deploy the Webhook:
1. Create a service and deployment for the webhook server.
2. Ensure the server listens for AdmissionReview requests and modifies the pod spec.

---

### 3. Setting Up a Validating Admission Webhook

#### Create a Validating Webhook Configuration:
Save the following YAML as `validating-webhook.yaml`:

```yaml
apiVersion: admissionregistration.k8s.io/v1
kind: ValidatingWebhookConfiguration
metadata:
  name: example-validating-webhook
webhooks:
  - name: validate.example.com
    clientConfig:
      service:
        name: validating-webhook-service
        namespace: default
        path: /validate
      caBundle: <BASE64_ENCODED_CA_CERT>
    rules:
      - operations: ["CREATE", "UPDATE"]
        apiGroups: [""]
        apiVersions: ["v1"]
        resources: ["pods"]
    admissionReviewVersions: ["v1", "v1beta1"]
    sideEffects: None
```

#### Deploy the Webhook:
1. Create a service and deployment for the webhook server.
2. Ensure the server listens for AdmissionReview requests and validates the pod spec.

---

### 4. Testing the Admission Controllers

#### Deploy a Pod to Trigger Admission Webhooks:
Save the following as `test-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: nginx
    image: nginx
```

#### Apply the Pod:
```bash
kubectl apply -f test-pod.yaml
```

#### Check Logs:
Review logs from the webhook server to see admission requests and responses.

---