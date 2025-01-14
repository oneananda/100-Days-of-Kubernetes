﻿---

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


### 3. Creating a Custom Helm Chart

#### Create a New Chart:
```bash
helm create my-chart
```

This command creates a directory `my-chart` with a default structure:
- `Chart.yaml`: Chart metadata.
- `values.yaml`: Default configuration values.
- `templates/`: Kubernetes manifests as templates.

#### Modify the Chart:
1. Edit `values.yaml` to set default values for your application:
   ```yaml
   replicaCount: 2
   image:
     repository: nginx
     tag: "1.21.6"
     pullPolicy: IfNotPresent
   ```
2. Update the template in `templates/deployment.yaml` to use the values.

#### Install the Custom Chart:
```bash
helm install my-app ./my-chart
```

#### Verify the Deployment:
```bash
helm list
kubectl get all
```

---

### 4. Upgrading and Rolling Back Applications

#### Upgrade the Release:
Modify `values.yaml` and apply the changes:
```bash
helm upgrade my-app ./my-chart
```

#### Roll Back to a Previous Release:
```bash
helm rollback my-app 1
```

---

### 📝 Document Your Progress

In your `day20.md` file, record:
- The steps for using Helm to deploy and manage applications.
- Custom chart creation details, including any modifications to the default templates.
- Observations on how Helm simplifies Kubernetes resource management.

---

### 🎯 Outcome for Day 20

By the end of Day 20, you should:
1. Understand the basics of Helm and its role in Kubernetes.
2. Be able to deploy applications using existing Helm charts.
3. Know how to create and customize Helm charts for your applications.
4. Manage application upgrades and rollbacks effectively using Helm.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Helm](https://helm.sh/docs/)
- [Helm Chart Repository](https://artifacthub.io/)

---