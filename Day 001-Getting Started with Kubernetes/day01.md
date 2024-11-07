---

## Day 1: Introduction to Kubernetes and Setting Up a Local Development Environment

### 📘 Overview

Today’s goal is to get familiar with Kubernetes and understand what it is, why it’s used, and how it can help in managing containerized applications. By the end of the day, you’ll have a local Kubernetes environment set up to start experimenting with clusters, nodes, and pods. We’ll use either **Minikube** or **Kind** (Kubernetes in Docker) as our local Kubernetes environment.

---

### 🌐 What is Kubernetes?

Kubernetes, often abbreviated as K8s, is an open-source platform that automates the deployment, scaling, and management of containerized applications. Originally developed by Google and now maintained by the Cloud Native Computing Foundation (CNCF), Kubernetes enables developers and operations teams to manage their applications in a way that provides resilience, scalability, and flexibility.

### Key Concepts to Understand:
1. **Containers**: Lightweight, standalone executables that contain everything needed to run a piece of software.
2. **Cluster**: A set of worker machines (nodes) that run containerized applications, managed by a master node.
3. **Nodes**: The machines in the cluster. Each node can host one or more pods.
4. **Pods**: The smallest, most basic deployable units of computing in Kubernetes, which encapsulate one or more containers.

---

### 🔧 Setting Up the Environment

To get hands-on with Kubernetes, we’ll set up a local development environment using one of the following tools:

#### Option 1: Minikube

Minikube runs a local Kubernetes cluster on your machine and is a great way to test Kubernetes without needing a full cloud setup.

1. **Install Minikube**:
   - **Windows**: Download the installer from [Minikube’s releases page](https://github.com/kubernetes/minikube/releases).
   - **macOS**: Use Homebrew with `brew install minikube`.
   - **Linux**: Follow instructions for your distribution on the [official docs](https://minikube.sigs.k8s.io/docs/start/).

2. **Start Minikube**:
   ```bash
   minikube start
   ```

3. **Verify Installation**:
   ```bash
   kubectl get nodes
   ```

   This command should list the nodes in your Minikube cluster, confirming that Kubernetes is running.

**Summary:**

Install the following 

- [minikube start](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/#install-nonstandard-package-tools)

#### Option 2: Kind (Kubernetes in Docker)

Kind is another lightweight way to run Kubernetes clusters locally by using Docker containers.

1. **Install Kind**:
   - **Windows/macOS/Linux**: Download the Kind binary from the [Kind GitHub page](https://kind.sigs.k8s.io/), or install it via Homebrew on macOS with `brew install kind`.

2. **Create a Cluster**:
   ```bash
   kind create cluster
   ```

3. **Verify Installation**:
   ```bash
   kubectl get nodes
   ```

   You should see a list of nodes, indicating that Kubernetes is up and running in your Docker containers.

---

### 📋 Verifying `kubectl` Setup

`kubectl` is the command-line tool for interacting with Kubernetes clusters. After installing Minikube or Kind, check if `kubectl` is properly configured:

```bash
kubectl cluster-info
```

This command should display information about your Kubernetes cluster, like the API server URL and other details.

---

### 📝 Document Your Setup

Create a `day01.md` file in your repository with notes on:
- **Installation steps**: List any specific steps you took or issues encountered.
- **Command outputs**: Include the output of `kubectl get nodes` and `kubectl cluster-info` to verify the setup.
- **Screenshots**: Optional, but screenshots can help show your initial setup progress.

---

### 🎯 Outcome for Day 1

By the end of Day 1, you should have:
1. A local Kubernetes cluster running with Minikube or Kind.
2. Verified your setup with basic `kubectl` commands.
3. An understanding of what Kubernetes is and its fundamental concepts.

### 🔗 Resources for Further Learning

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/start/)
- [Kind Documentation](https://kind.sigs.k8s.io/)

---

