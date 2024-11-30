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