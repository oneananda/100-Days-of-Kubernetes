---

## Day 24: Kubernetes Probes — Ensuring Application Health

### 📘 Overview

**Kubernetes Probes** are used to monitor the health of containers in a pod. They help ensure applications are running correctly by checking their state and restarting containers if necessary. Today’s session focuses on implementing **Liveness**, **Readiness**, and **Startup** probes.

---


### What are Kubernetes Probes?

1. **Liveness Probe**:
   - Ensures the application is still running.
   - Restarts the container if it becomes unresponsive or crashes.

2. **Readiness Probe**:
   - Checks if the application is ready to serve traffic.
   - Helps prevent sending traffic to unhealthy pods.

3. **Startup Probe**:
   - Ensures the application has started successfully before other probes are triggered.
   - Useful for applications with long initialization times.

---

### Key Concepts

1. **Probe Types**:
   - **HTTP Probe**: Sends an HTTP request to a specified path and port.
   - **TCP Probe**: Checks if a TCP port is open.
   - **Exec Probe**: Runs a command inside the container and checks the exit code.

2. **Probe Actions**:
   - **Success**: The container is considered healthy.
   - **Failure**: The container is restarted or removed from service based on the probe type.

---


### Hands-On with Probes

In today’s session, we’ll configure and use different probes to monitor the health of a sample application.

---

### 1. Implementing a Liveness Probe

Deploy a pod with a liveness probe. Save the following YAML as `liveness-probe.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-probe-demo
spec:
  containers:
  - name: liveness-probe-container
    image: busybox
    args:
    - /bin/sh
    - -c
    - |
      touch /tmp/healthy;
      sleep 30;
      rm -rf /tmp/healthy;
      sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```

#### Apply the Pod:
```bash
kubectl apply -f liveness-probe.yaml
```

#### Verify the Probe:
```bash
kubectl describe pod liveness-probe-demo
kubectl get events
```

---

### 2. Implementing a Readiness Probe

Deploy a pod with a readiness probe. Save the following YAML as `readiness-probe.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: readiness-probe-demo
spec:
  containers:
  - name: readiness-probe-container
    image: nginx
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
```

#### Apply the Pod:
```bash
kubectl apply -f readiness-probe.yaml
```

#### Verify the Probe:
```bash
kubectl describe pod readiness-probe-demo
kubectl get pods
```

---

### 3. Implementing a Startup Probe

Deploy a pod with a startup probe. Save the following YAML as `startup-probe.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: startup-probe-demo
spec:
  containers:
  - name: startup-probe-container
    image: busybox
    args:
    - /bin/sh
    - -c
    - sleep 60; echo Application started
    startupProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 10
      failureThreshold: 30
      periodSeconds: 10
```

#### Apply the Pod:
```bash
kubectl apply -f startup-probe.yaml
```

#### Verify the Probe:
```bash
kubectl describe pod startup-probe-demo
kubectl get pods
```

---

### 4. Combining Probes

You can combine probes for comprehensive health checks. For example, a pod can use:
- **Startup Probe**: To ensure initialization is complete.
- **Liveness Probe**: To check runtime health.
- **Readiness Probe**: To check service readiness.

---

### 📝 Document Your Progress

In your `day24.md` file, record:
- YAML configurations for Liveness, Readiness, and Startup probes.
- Steps to configure and verify each probe type.
- Insights or challenges when combining probes.

---

### 🎯 Outcome for Day 24

By the end of Day 24, you should:
1. Understand the role of probes in ensuring application health.
2. Be able to implement Liveness, Readiness, and Startup probes.
3. Know how to troubleshoot and combine probes for robust monitoring.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
- [Kubernetes Health Check Best Practices](https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/)

---