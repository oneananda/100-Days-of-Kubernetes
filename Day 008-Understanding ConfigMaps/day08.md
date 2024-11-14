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

### 3. Using ConfigMaps in Pods

ConfigMap data can be injected into Pods in various ways, including as environment variables and mounted files.

#### Inject ConfigMap Data as Environment Variables

Create a Deployment using the `my-config` ConfigMap. Save this as `nginx-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        envFrom:
        - configMapRef:
            name: my-config
```

- **envFrom**: Specifies the ConfigMap to load as environment variables.

Apply the YAML file:

```bash
kubectl apply -f nginx-deployment.yaml
```

#### Access ConfigMap Data in the Container

To verify the ConfigMap data is available as environment variables, run a shell inside the Pod:

```bash
kubectl exec -it <pod-name> -- env
```

Look for `APP_COLOR`, `APP_MODE`, and any other ConfigMap keys to confirm they’re loaded as environment variables.

#### Mount ConfigMap as a Volume

You can also mount a ConfigMap as a volume to make configuration data available as files.

Edit the `nginx-deployment.yaml` to add a volume mount:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: config-volume
          mountPath: /etc/config
      volumes:
      - name: config-volume
        configMap:
          name: my-config
```

- **volumeMounts**: Specifies where to mount the ConfigMap in the container’s filesystem.
- **volumes**: References the ConfigMap by name, making its data available as files in the container.

Apply the updated YAML file:

```bash
kubectl apply -f nginx-deployment.yaml
```

In the container, each ConfigMap key will appear as a file in `/etc/config`, with the content being the corresponding value.

---

### 4. Updating ConfigMaps

To update a ConfigMap, modify the YAML file or recreate it with new values. Note that **Pods do not automatically detect ConfigMap changes**, so you’ll need to recreate or restart Pods for updates to take effect.

To delete and recreate a ConfigMap:

```bash
kubectl delete configmap my-config
kubectl apply -f my-config.yaml
```

Or, edit it directly with:

```bash
kubectl edit configmap my-config
```

---

