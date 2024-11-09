Examples of using `kubectl create` to create various Kubernetes resources:

1. **Create a Pod**:
   ```bash
   kubectl create pod my-pod --image=nginx
   ```

2. **Create a Deployment**:
   ```bash
   kubectl create deployment my-deployment --image=nginx
   ```

3. **Create a Service**:
   - **ClusterIP** (default):
     ```bash
     kubectl create service clusterip my-service --tcp=80:80
     ```
   - **NodePort**:
     ```bash
     kubectl create service nodeport my-service --tcp=80:80
     ```
   - **LoadBalancer**:
     ```bash
     kubectl create service loadbalancer my-service --tcp=80:80
     ```

4. **Create a Namespace**:
   ```bash
   kubectl create namespace my-namespace
   ```

5. **Create a ConfigMap**:
   ```bash
   kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2
   ```

6. **Create a Secret**:
   ```bash
   kubectl create secret generic my-secret --from-literal=username=admin --from-literal=password='s3cr3t'
   ```

7. **Create a Job**:
   ```bash
   kubectl create job my-job --image=busybox -- echo "Hello, Kubernetes!"
   ```

8. **Create a CronJob**:
   ```bash
   kubectl create cronjob my-cronjob --schedule="*/5 * * * *" --image=busybox -- echo "Hello from CronJob!"
   ```

9. **Create a Persistent Volume Claim**:
   ```bash
   kubectl create pvc my-pvc --storage=1Gi
   ```

10. **Create a Resource from a YAML file**:
    ```bash
    kubectl create -f my-resource.yaml
    ```
