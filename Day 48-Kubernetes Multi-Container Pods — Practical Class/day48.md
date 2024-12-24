---

## Day 48: Kubernetes Multi-Container Pods — Practical Class

### 📘 Overview

Kubernetes pods can run multiple containers that share resources such as storage and network. **Multi-Container Pods** enable containers to work closely together within the same pod, supporting use cases like sidecar patterns, logging agents, and helper containers.

This session focuses on creating multi-container pods, understanding container communication within a pod, and implementing common patterns.

---

### Objectives

1. Understand the use cases for multi-container pods.
2. Create and configure multi-container pods.
3. Enable communication between containers within a pod.
4. Implement sidecar and adapter patterns.

---

### Key Concepts

1. **Multi-Container Pod**:
   - A pod that runs two or more containers sharing the same storage, network namespace, and IP address.

2. **Container Communication**:
   - Containers in the same pod communicate via `localhost`.

3. **Common Patterns**:
   - **Sidecar Pattern**: Augments the functionality of the main container.
   - **Adapter Pattern**: Acts as a translator between the main container and external systems.
   - **Ambassador Pattern**: Manages connections to external systems.

---

### Practical Exercises

---

### 1. Creating a Multi-Container Pod

#### Step 1: Define a Multi-Container Pod
Create a `multi-container-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-demo
spec:
  containers:
  - name: main-app
    image: nginx:1.19
    ports:
    - containerPort: 80
  - name: sidecar-container
    image: busybox
    command: ["sh", "-c", "while true; do echo 'Sidecar: $(date)' >> /var/log/app.log; sleep 5; done"]
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log
  volumes:
  - name: shared-logs
    emptyDir: {}
```

Apply the configuration:
```bash
kubectl apply -f multi-container-pod.yaml
```

Verify the pod:
```bash
kubectl get pods
```

---

### 2. Testing Container Communication

#### Step 1: Access the Main Container
Execute into the main container:
```bash
kubectl exec -it multi-container-demo -c main-app -- sh
```

Check the shared log file:
```bash
cat /var/log/app.log
```

#### Step 2: Access the Sidecar Container
Execute into the sidecar container:
```bash
kubectl exec -it multi-container-demo -c sidecar-container -- sh
```

View the logs being written:
```bash
tail -f /var/log/app.log
```

---

### 3. Implementing the Sidecar Pattern

#### Step 1: Enhance the Sidecar Container
Update the pod to include a sidecar container for log rotation:
```yaml
  - name: log-rotator
    image: busybox
    command: ["sh", "-c", "while true; do mv /var/log/app.log /var/log/app-$(date +%s).log; sleep 60; done"]
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log
```

Apply the updated configuration:
```bash
kubectl apply -f multi-container-pod.yaml
```

Verify log rotation by checking `/var/log` in the main or sidecar container.

---

### 4. Cleanup

Remove the pod:
```bash
kubectl delete pod multi-container-demo
```

---

### Best Practices for Multi-Container Pods

1. **Shared Resources**:
   - Use `emptyDir` or shared volumes for collaboration between containers.

2. **Design Principles**:
   - Each container should have a focused responsibility to avoid overloading individual containers.

3. **Monitoring and Debugging**:
   - Use centralized logging and monitoring tools to observe all containers within the pod.

4. **Sidecar Responsibilities**:
   - Common sidecar use cases include logging, monitoring, data preprocessing, and API management.

---
