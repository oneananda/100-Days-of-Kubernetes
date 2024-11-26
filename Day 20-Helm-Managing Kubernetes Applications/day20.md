---

## Day 20: Helm — Managing Kubernetes Applications

### 📘 Overview

**Helm** is a powerful package manager for Kubernetes that simplifies the deployment and management of applications by using predefined templates, called Helm charts. Today’s session focuses on understanding Helm, creating Helm charts, and deploying applications using Helm.

---

### What is Helm?

- **Helm** is the Kubernetes equivalent of a package manager like `apt` or `yum`.
- It helps in managing Kubernetes manifests using a templated configuration approach.
- Helm organizes Kubernetes resources into a single unit called a **chart**, which can be easily shared and reused.

---

### Key Concepts

1. **Helm Chart**:
   - A collection of files that describe a set of Kubernetes resources.
   - Includes templates, values, and metadata.

2. **Helm Release**:
   - A running instance of a Helm chart deployed in a Kubernetes cluster.

3. **Repository**:
   - A place where Helm charts are stored and shared, such as the official Helm Hub.

4. **Values**:
   - Configuration settings for the chart, defined in `values.yaml`.

---

### Hands-On with Helm

In today’s session, we’ll install Helm, use a pre-existing chart, and create a custom chart.

---

### 1. Installing Helm

Download and install Helm:

#### On macOS:
```bash
brew install helm
```

#### On Linux:
```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

#### Verify the Installation:
```bash
helm version
```

---


### 2. Using a Pre-Existing Chart

Deploy an NGINX application using an existing chart:

#### Add the Helm Stable Repository:
```bash
helm repo add stable https://charts.helm.sh/stable
```

#### Update the Chart Repository:
```bash
helm repo update
```

#### Install the NGINX Chart:
```bash
helm install my-nginx stable/nginx-ingress
```

#### Verify the Installation:
```bash
helm list
kubectl get pods
```

---