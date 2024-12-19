---

## Day 43: Kubernetes Debugging and Troubleshooting — Practical Class

### 📘 Overview

Debugging and troubleshooting are critical skills for managing Kubernetes clusters and workloads. This session focuses on identifying and resolving issues in pods, services, and other cluster resources using built-in Kubernetes tools and commands.

---


### Objectives

1. Understand the tools and commands for debugging in Kubernetes.
2. Diagnose and fix issues with pods, deployments, and services.
3. Use logs and events to identify root causes of failures.
4. Debug live pods using tools like `kubectl exec` and ephemeral containers.

---

### Key Concepts

1. **Kubernetes Events**:
   - Provide real-time information about what’s happening in the cluster.
   - Useful for diagnosing resource creation, updates, and errors.

2. **Pod Logs**:
   - Capture application output for debugging.
   - Can be viewed using `kubectl logs`.

3. **Ephemeral Containers**:
   - Temporary containers added to running pods for debugging.
   - Useful for troubleshooting without modifying the pod’s configuration.

4. **Common Issues**:
   - Image pull errors.
   - Pod scheduling issues.
   - Misconfigured services or ingress.

---


### Practical Exercises

---

### 1. Debugging Pods

#### Step 1: Simulate a CrashLoopBackOff Error
Create a `pod-crashloop.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: crashloop-demo
spec:
  containers:
  - name: demo-container
    image: busybox
    command: ["sh", "-c", "echo 'Simulating crash...' && exit 1"]
```

Apply the pod:
```bash
kubectl apply -f pod-crashloop.yaml
```

#### Step 2: Check Pod Status and Events
View pod status:
```bash
kubectl get pods
kubectl describe pod crashloop-demo
```

Analyze events for errors:
```bash
kubectl get events --field-selector involvedObject.name=crashloop-demo
```

#### Step 3: Debug Using Logs
Attempt to view logs:
```bash
kubectl logs crashloop-demo
```

---

### 2. Debugging Image Pull Errors

#### Step 1: Simulate an ImagePullBackOff Error
Create a `pod-imagepull.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: imagepull-demo
spec:
  containers:
  - name: demo-container
    image: nonexistent-image:latest
```

Apply the pod:
```bash
kubectl apply -f pod-imagepull.yaml
```

#### Step 2: Check Pod Events
Describe the pod to find the error:
```bash
kubectl describe pod imagepull-demo
```

Resolve the issue by updating the pod with a valid image:
```yaml
kubectl edit pod imagepull-demo
```

---

### 3. Debugging Networking Issues

#### Step 1: Deploy a Pod and Service
Create a `network-debug.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

Apply the configuration:
```bash
kubectl apply -f network-debug.yaml
```

#### Step 2: Verify Service Connectivity
Check the service and pod status:
```bash
kubectl get pods
kubectl get svc nginx-service
```

Test connectivity using `kubectl exec`:
```bash
kubectl exec -it nginx-pod -- curl http://nginx-service
```

If there’s an issue, inspect pod events and logs:
```bash
kubectl logs nginx-pod
kubectl describe pod nginx-pod
```

---

### 4. Debugging Live Pods with Ephemeral Containers

#### Step 1: Add an Ephemeral Container
Add a temporary container for debugging:
```bash
kubectl debug nginx-pod --image=busybox --target=nginx
```

Check the ephemeral container:
```bash
kubectl get pods nginx-pod
```

Execute commands in the ephemeral container:
```bash
kubectl exec -it nginx-pod -c debugger -- sh
```

#### Step 2: Remove the Ephemeral Container
Ephemeral containers are automatically removed when the pod is terminated.

---

### 5. Cleanup

Remove all resources:
```bash
kubectl delete pod crashloop-demo
kubectl delete pod imagepull-demo
kubectl delete -f network-debug.yaml
```

---

### Best Practices for Debugging

1. **Use Namespaces**:
   - Isolate resources in namespaces for better clarity during troubleshooting.

2. **Monitor Events**:
   - Regularly monitor cluster events for early detection of issues.

3. **Leverage Logs**:
   - Use centralized logging tools like Elasticsearch and Kibana for application logs.

4. **Automate Monitoring**:
   - Integrate monitoring tools like Prometheus and Grafana to observe resource health.

5. **Ephemeral Containers**:
   - Use ephemeral containers for safe and efficient debugging of live pods.

---


### 📝 Document Your Progress

In your `day43.md` file, record:
- Steps taken to debug different issues.
- Observations from pod events and logs.
- Challenges faced and how they were resolved.
- Tools and commands used for troubleshooting.

---

### 🎯 Outcome for Day 43

By the end of Day 43, you should:
1. Identify and resolve common issues with Kubernetes resources.
2. Debug pods using events, logs, and ephemeral containers.
3. Troubleshoot networking and configuration errors.
4. Apply best practices to improve debugging efficiency.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Troubleshooting](https://kubernetes.io/docs/tasks/debug/debug-application/)
- [Debugging with Ephemeral Containers](https://kubernetes.io/docs/concepts/workloads/pods/ephemeral-containers/)
- [Kubernetes Networking Troubleshooting](https://kubernetes.io/docs/tasks/debug/debug-cluster/network/)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

---