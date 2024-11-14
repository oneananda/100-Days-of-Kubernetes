---

## Day 8: Understanding ConfigMaps for External Configuration

### 📘 Overview

**ConfigMaps** in Kubernetes provide a way to store and manage non-sensitive configuration data, allowing you to keep configuration separate from application code. This flexibility makes applications more portable and easier to manage, especially when working with multiple environments (e.g., development, staging, production).

---

### What is a ConfigMap?

A **ConfigMap** is an API object that allows you to store configuration data as key-value pairs. This data can then be injected into Pods as environment variables, command-line arguments, or configuration files. ConfigMaps make it easy to update application settings without rebuilding or redeploying the application.

---

### Key Features of ConfigMaps

1. **Environment-Agnostic Configurations**: Store configuration data separately from code, making it easier to manage across environments.
2. **Flexible Usage**: Inject configuration as environment variables, files, or command-line arguments.
3. **Centralized Management**: Update configuration in a centralized ConfigMap without modifying the application code or image.

---

### Hands-On with ConfigMaps

Let’s explore creating, inspecting, and using ConfigMaps in various ways to configure an application.

---

### 1. Creating ConfigMaps

You can create a ConfigMap directly from the command line or from a YAML file.

#### Example: Create a ConfigMap from Literal Values

You can create a ConfigMap with literal key-value pairs directly from the command line:

```bash
kubectl create configmap my-config --from-literal=APP_COLOR=blue --from-literal=APP_MODE=production
```

#### Example: Create a ConfigMap from a File

Alternatively, you can store configuration data in a file and create a ConfigMap from it.

Create a file named `app-config.properties` with the following content:

```
APP_COLOR=green
APP_MODE=staging
```

Then, create a ConfigMap from the file:

```bash
kubectl create configmap my-config --from-file=app-config.properties
```

#### Example: Create a ConfigMap from a YAML File

You can also define a ConfigMap in a YAML file for more flexibility. Create a file named `my-config.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  APP_COLOR: "red"
  APP_MODE: "development"
  MAX_RETRIES: "5"
```

Apply the YAML file to create the ConfigMap:

```bash
kubectl apply -f my-config.yaml
```

---

### 2. Inspecting ConfigMaps

Once created, you can inspect ConfigMaps using `kubectl` commands.

#### List All ConfigMaps

```bash
kubectl get configmaps
```

#### View ConfigMap Details

```bash
kubectl describe configmap my-config
```

This command shows the content and metadata of the ConfigMap.

#### Display ConfigMap Data

To view the ConfigMap data directly, use:

```bash
kubectl get configmap my-config -o yaml
```

This will display the ConfigMap in YAML format, showing all key-value pairs.

---


