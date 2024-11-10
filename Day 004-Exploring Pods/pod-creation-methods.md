# Pod creation methods

There are several ways to create a pod in Kubernetes, each suited to different needs and use cases.

### 1. **Using a YAML Configuration File**
   - The most common way to create a pod is by defining it in a YAML file and then applying the file with `kubectl`.
   - Example YAML file (`pod.yaml`):
     ```yaml
     apiVersion: v1
     kind: Pod
     metadata:
       name: my-pod
     spec:
       containers:
       - name: my-container
         image: nginx
     ```
   - To create the pod, run:
     ```bash
     kubectl apply -f pod.yaml
     ```

### 2. **Using `kubectl run` Command**
   - You can create a pod directly from the command line using `kubectl run`, which is a quick way to create a simple pod.
   - Example:
     ```bash
     kubectl run my-pod --image=nginx
     ```
   - Note: This command is typically used for testing purposes, and in production, using YAML files for configuration is preferred for better management and version control.


