---

## Day 7: Services — Exposing Applications Within and Outside the Cluster

### 📘 Overview

**Services** in Kubernetes provide stable endpoints for applications running in Pods, enabling reliable access to them. Since Pods are ephemeral and can be recreated, Services ensure that applications remain accessible even if the underlying Pods change. Services also enable communication between Pods within the cluster and expose applications to the outside world.

---

### What is a Service?

A Service in Kubernetes provides a consistent way to access a set of Pods, abstracting away their IP addresses. Services use **selectors** to group Pods based on labels, allowing traffic to be automatically routed to the right Pods even if they are replaced.

Key types of Services include:
1. **ClusterIP**: Exposes the Service to other Pods within the cluster.
2. **NodePort**: Exposes the Service on a specific port on each node, making it accessible outside the cluster.
3. **LoadBalancer**: Provisions an external load balancer to distribute traffic across Pods (usually available in cloud environments).
4. **ExternalName**: Maps a Service to an external DNS name.

---

### Key Features of Services

1. **Service Discovery**: Provides a stable network endpoint, allowing other applications to connect without worrying about Pod IP changes.
2. **Load Balancing**: Distributes traffic among all Pods in the Service.
3. **Port Mapping**: Services provide port mapping options to make it easier to expose applications on specific ports.

---

### Hands-On with Services

Let’s explore creating and inspecting different types of Services using `kubectl` and YAML configurations.

---

### 1. Creating a ClusterIP Service

A **ClusterIP** Service allows communication only within the cluster, providing an internal IP for other Pods to access the application.

#### Step 1: Create a Deployment

If you haven’t already, create a Deployment to represent the application:

```yaml
# Save this as nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

Apply the Deployment:

```bash
kubectl apply -f nginx-deployment.yaml
```

#### Step 2: Create a ClusterIP Service

Create a file named `nginx-service.yaml` with the following configuration:

```yaml
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
  type: ClusterIP
```

- **selector**: Matches the Pods labeled `app: nginx`, directing traffic to them.
- **port**: The port on which the Service is exposed.
- **targetPort**: The port on the container to which traffic is directed.
- **type: ClusterIP**: Restricts access to within the cluster.

Apply the Service:

```bash
kubectl apply -f nginx-service.yaml
```

#### Step 3: Test the ClusterIP Service

To access the Service from within the cluster, you can use `kubectl` to execute commands in a different Pod and reach the Service by its name:

```bash
kubectl run test-pod --image=busybox --rm -it -- /bin/sh
# Inside the pod, test the connection
wget -qO- nginx-service
```

---

### 2. Creating a NodePort Service

A **NodePort** Service exposes the application on each node’s IP at a specific port, allowing external traffic to access the application.

Edit the `nginx-service.yaml` file and change the type to `NodePort`:

```yaml
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
    nodePort: 30007  # Choose a port in the range 30000-32767
  type: NodePort
```

Apply the changes:

```bash
kubectl apply -f nginx-service.yaml
```

#### Access the NodePort Service

Find the IP of any node in the cluster (or use `minikube ip` if using Minikube) and access the application at `http://<NodeIP>:30007`.

---

### 3. Creating a LoadBalancer Service (Cloud Environment Only)

A **LoadBalancer** Service is typically used in cloud environments (e.g., GCP, AWS) and provides an external load balancer that distributes traffic across the Pods.

Change the type in `nginx-service.yaml` to `LoadBalancer`:

```yaml
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
  type: LoadBalancer
```

Apply the changes:

```bash
kubectl apply -f nginx-service.yaml
```

In a cloud environment, this will provision a load balancer and assign it an external IP. You can access the application through this IP.

---
