---

## Day 3: Getting Hands-on with `kubectl` Commands (create, get, describe, delete)

### 📘 Overview

`kubectl` is the command-line tool used to interact with Kubernetes clusters. It allows you to create, manage, and troubleshoot resources within your cluster. Today, we’ll focus on the essential `kubectl` commands: `create`, `get`, `describe`, and `delete`. By the end of this day, you’ll be familiar with using these commands to manage Kubernetes resources like Pods, Deployments, and Services.

---

We’ll start using `kubectl` commands to interact with your Kubernetes cluster.


### Setting up for Hands-on Practice

Before running commands, make sure you have:
1. A running Kubernetes cluster (e.g., through Minikube or Kind from Day 1).
2. `kubectl` installed and configured to interact with your cluster.

We can start using the kubernetes-dashboard by issuing the following command

```bash
minikube start
```

We can check that `kubectl` is configured correctly by running:
```bash
kubectl cluster-info
```

---

### Core `kubectl` Commands

We’ll use `kubectl` to manage Pods and Deployments in the following examples.

---

### 1. `kubectl create`

The `create` command allows you to create resources directly from the command line. You can create resources from manifest files (YAML files) or directly via command options.

#### Example: Create a Pod
Create a simple Nginx Pod directly from the command line:
```bash
kubectl run nginx-pod --image=nginx
```

#### Example: Create a Deployment
A Deployment manages a set of identical Pods, providing features like rolling updates and scaling.

```bash
kubectl create deployment nginx-deployment --image=nginx
```

#### Example: Create from a YAML file
You can also create resources by specifying a YAML configuration file. Save the following content in a file called `nginx-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx
```

Then, create the Pod using:
```bash
kubectl create -f nginx-pod.yaml
```

#### Example: Create a Deployment based on hosted image

```bash
kubectl create deployment hello-node --image=registry.k8s.io/e2e-test-images/agnhost:2.39 -- /agnhost netexec --http-port=8080
```

The above command creates a deployment named `hello-node` in Kubernetes with a specific container image and custom entry point.

- **`kubectl create deployment hello-node`**: This part uses `kubectl` to create a deployment called `hello-node`. A deployment manages a set of replicated Pods and ensures the desired number of them are running at all times.

- **`--image=registry.k8s.io/e2e-test-images/agnhost:2.39`**: This specifies the container image to use. In this case, it's an image hosted on `registry.k8s.io` with the tag `2.39` from the `e2e-test-images` repository. `agnhost` is a general-purpose test image in Kubernetes that can perform various functions like echoing data, running a simple server, etc.

- **`-- /agnhost netexec --http-port=8080`**: This part overrides the default command for the container. It runs the `agnhost` executable with the `netexec` command, which starts a server that listens on the specified `http-port` (`8080`). This server can be used for networking tests and troubleshooting, responding to HTTP requests on port 8080.

In summary, this command creates a deployment with one replica of a pod running the `agnhost` container, which is set to respond to HTTP requests on port 8080, allowing for network connectivity testing or simple HTTP responses.

#### Minikube dashboard

The **Minikube dashboard** is a web-based user interface for Kubernetes that provides an overview of the cluster's resources, enabling users to monitor and manage their applications within the cluster easily.

### How to Access the Minikube Dashboard

1. **Start Minikube** (if it’s not running):
   ```bash
   minikube start
   ```

2. **Launch the Dashboard**:
   ```bash
   minikube dashboard
   ```

---


