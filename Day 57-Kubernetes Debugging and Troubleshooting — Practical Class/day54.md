---

## Day 57: Kubernetes Debugging and Troubleshooting — Practical Class

### 📘 Overview

Debugging and troubleshooting Kubernetes clusters are critical skills for maintaining application performance and cluster health. This session focuses on identifying and resolving issues related to pods, nodes, networking, and resource utilization using native Kubernetes tools and techniques.

---

### Objectives

1. Learn systematic approaches to troubleshooting Kubernetes clusters.
2. Use Kubernetes debugging tools like `kubectl debug`, logs, and events.
3. Resolve common issues with pods, nodes, and networking.
4. Implement best practices to prevent recurring issues.

---

### Key Concepts

1. **Pod-Level Issues**:
   - Crashed or pending pods due to misconfigured resources, image pull errors, or application bugs.

2. **Node-Level Issues**:
   - Nodes in `NotReady` or `Unknown` state due to resource exhaustion or connectivity problems.

3. **Networking Issues**:
   - Pod-to-pod or pod-to-service communication failures caused by misconfigured network policies or DNS problems.

4. **Resource Management**:
   - Overcommitted resources leading to evictions or throttling.

---

### Practical Exercises

---

#### 1. Debugging Pods

##### Step 1: Check Pod Status
List all pods and their statuses:
```bash
kubectl get pods -o wide
```

##### Step 2: Investigate Events
Inspect events for failed pods:
```bash
kubectl describe pod <pod-name>
```

##### Step 3: Debug Containers
Use `kubectl debug` to create a troubleshooting container in the same pod namespace:
```bash
kubectl debug pod/<pod-name> --image=busybox --stdin --tty
```
Check the file system, environment variables, or network connectivity.

##### Step 4: Analyze Logs
View logs for a specific container:
```bash
kubectl logs <pod-name> -c <container-name>
```

---

#### 2. Debugging Nodes

##### Step 1: Check Node Status
Inspect node conditions:
```bash
kubectl get nodes
kubectl describe node <node-name>
```

##### Step 2: Analyze Resource Usage
Use `kubectl top` to check resource consumption:
```bash
kubectl top nodes
kubectl top pods --namespace=<namespace>
```

##### Step 3: Resolve Node Issues
- Restart the kubelet service if a node is unresponsive:
  ```bash
  sudo systemctl restart kubelet
  ```
- Drain a problematic node for maintenance:
  ```bash
  kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
  ```

---

#### 3. Debugging Networking

##### Step 1: Test Pod-to-Pod Communication
Deploy a testing pod with tools like `curl` or `ping`:
```bash
kubectl run debug-pod --image=busybox --restart=Never -- sleep 3600
```

Exec into the pod and test connectivity:
```bash
kubectl exec -it debug-pod -- sh
ping <target-pod-IP>
curl http://<service-name>:<port>
```

##### Step 2: Inspect Network Policies
Check for network policies affecting communication:
```bash
kubectl get networkpolicies -n <namespace>
kubectl describe networkpolicy <policy-name>
```

---

#### 4. Resolving Common Issues

##### Image Pull Errors
Check pod events for error messages:
```bash
kubectl describe pod <pod-name>
```
Verify image repository, tag, and credentials.

##### Misconfigured Resources
Update resource requests and limits in the pod specification:
```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "256Mi"
```

##### DNS Resolution Issues
Verify the DNS configuration:
```bash
kubectl exec -it <pod-name> -- nslookup <service-name>
```

---

#### 5. Using Advanced Debugging Tools

##### Step 1: Using `kubectl debug` with Node Troubleshooting
Create a debugging pod on a node:
```bash
kubectl debug node/<node-name> --image=busybox --stdin --tty
```

##### Step 2: Inspecting Metrics with Metrics Server
Install the Metrics Server if not already installed:
```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

View cluster-wide metrics:
```bash
kubectl top nodes
kubectl top pods
```

##### Step 3: Using Diagnostic Tools
Deploy tools like `k9s`, `stern`, or `Lens` for cluster monitoring and debugging.

---

#### 6. Cleanup

Remove the debugging resources:
```bash
kubectl delete pod debug-pod
```

---

### Best Practices for Debugging Kubernetes

1. **Use Namespace Isolation**:
   - Debug resources in their namespaces to avoid cross-contamination.

2. **Leverage Events and Logs**:
   - Analyze Kubernetes events and container logs for quick issue resolution.

3. **Enable Resource Monitoring**:
   - Use tools like Prometheus and Grafana for proactive monitoring.

4. **Automate Health Checks**:
   - Implement liveness and readiness probes for early issue detection.

5. **Test Before Deployment**:
   - Use tools like Helm linting or CI/CD pipelines for configuration validation.

---

### 📝 Document Your Progress

In your `day57.md` file, include:
- Issues you identified and resolved.
- Tools and techniques used for debugging.
- Examples of custom debugging commands.
- Lessons learned and preventive measures applied.

---

### 🎯 Outcome for Day 57

By the end of this session, you will:
1. Debug and troubleshoot pods, nodes, and networking in Kubernetes.
2. Resolve common cluster issues systematically.
3. Use advanced tools to enhance debugging efficiency.

---

### 🔗 Additional Resources

- [Kubernetes Debugging with kubectl](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-running-pod/)
- [Kubernetes Troubleshooting Guide](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)
- [Lens: Kubernetes IDE](https://k8slens.dev/)
- [K9s: Kubernetes CLI Debugging Tool](https://k9scli.io/)

---