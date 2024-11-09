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

We can check that `kubectl` is configured correctly by running:
```bash
kubectl cluster-info
```

---

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

More kubectl create examples are found [here]("/Day%20003-Kubectl%20Commands/kubectl-create.md")

---

### 2. `kubectl get`

The `get` command displays information about resources in the cluster. You can view specific resource types or all resources in a namespace.

#### Example: Get Pods
```bash
kubectl get pods
```

This command lists all Pods in the current namespace, including their status and IPs.

#### Example: Get Deployments
```bash
kubectl get deployments
```

#### Example: Get Services
```bash
kubectl get services
```

#### Example: Get More Details
To get additional details, use the `-o wide` option to see more information, such as the node each Pod is running on:
```bash
kubectl get pods -o wide
```

#### Example: Get Specific Pod
To get a specific Pod:
```bash
kubectl get pod nginx-pod
```

---

### 3. `kubectl describe`

The `describe` command shows detailed information about a specific resource, including configuration details, events, and any errors. This is useful for troubleshooting.

#### Example: Describe a Pod
```bash
kubectl describe pod nginx-pod
```

This command shows the Pod's configuration, current state, events, and any error messages, which are helpful for debugging.

#### Example: Describe a Deployment
```bash
kubectl describe deployment nginx-deployment
```

This gives information on the Deployment's strategy, replicas, and events related to its Pods.

---

### 4. `kubectl delete`

The `delete` command removes resources from the cluster. Be cautious with `delete`, especially when deleting resources in production environments.

#### Example: Delete a Pod
```bash
kubectl delete pod nginx-pod
```

#### Example: Delete a Deployment
```bash
kubectl delete deployment nginx-deployment
```

#### Example: Delete from YAML file
If you created resources using a YAML file, you can delete them with:
```bash
kubectl delete -f nginx-pod.yaml
```

#### Example: Delete All Pods (Use Carefully)
```bash
kubectl delete pods --all
```

This command deletes all Pods in the current namespace. Be careful with commands like this in production environments.

---

### Hands-On Practice

1. **Create Resources**: Try creating a Pod and a Deployment using the `kubectl run` and `kubectl create` commands.
2. **List Resources**: Use `kubectl get` to list all Pods, Deployments, and Services, experimenting with different `-o` options.
3. **Inspect Resources**: Use `kubectl describe` to examine a Pod and a Deployment, paying attention to events and statuses.
4. **Delete Resources**: Clean up by deleting the Pod and Deployment you created.

---

### 📝 Document Your Progress

In your `day03.md` file, record:
- The `kubectl` commands you used and their output.
- Notes on any challenges faced or insights gained.
- Screenshots or code snippets (optional but useful for future reference).

---

### 🎯 Outcome for Day 3

By the end of Day 3, you should:
1. Be comfortable creating and deleting resources in Kubernetes.
2. Know how to list and inspect resources with `kubectl`.
3. Understand how to troubleshoot using `kubectl describe`.

### 🔗 Additional Resources

- [Kubernetes Documentation: kubectl CLI](https://kubernetes.io/docs/reference/kubectl/overview/)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

---


