---

## Day 14: StatefulSets — Managing Stateful Applications in Kubernetes

### 📘 Overview

While **Deployments** and **ReplicaSets** are excellent for stateless applications, **StatefulSets** are designed to manage stateful applications that require stable identities, persistent storage, and ordered deployment. StatefulSets ensure that Pods are created, deleted, and scaled in a controlled manner.

---

### What is a StatefulSet?

- A **StatefulSet** is a Kubernetes workload API object that manages stateful applications.
- It ensures:
  1. Stable, unique network identifiers for each Pod (e.g., `pod-0`, `pod-1`).
  2. Persistent storage with volume claims associated with each Pod.
  3. Ordered deployment, scaling, and termination.

---

### Key Features of StatefulSets

1. **Stable Pod Identifiers**: Each Pod gets a predictable hostname.
2. **Ordered Scaling**: Pods are created and terminated in sequence.
3. **Persistent Storage**: Pods retain data even after being recreated.
4. **Use Cases**:
   - Databases like MySQL, MongoDB.
   - Distributed systems like Kafka, Zookeeper.

---


### Hands-On with StatefulSets

Let’s create and manage a StatefulSet for a stateful application.

---

### 1. Creating a StatefulSet

#### Example: Deploying a StatefulSet for a Stateful Application

Create a YAML file named `statefulset.yaml`:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "web-service"
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx
          volumeMounts:
            - name: web-storage
              mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
    - metadata:
        name: web-storage
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
```

#### Apply the Configuration:
```bash
kubectl apply -f statefulset.yaml
```

#### Verify the StatefulSet:
```bash
kubectl get statefulsets
kubectl get pods -l app=web
```

---


### 2. Creating a Headless Service

StatefulSets often require a **headless service** for stable network identities.

Create a YAML file named `headless-service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  clusterIP: None
  selector:
    app: web
  ports:
    - protocol: TCP
      port: 80
```

#### Apply the Service:
```bash
kubectl apply -f headless-service.yaml
```

---


### 3. Accessing StatefulSet Pods

Each Pod in a StatefulSet has a predictable DNS name:

```
<statefulset-name>-<ordinal>.<service-name>
```

For example:
- `web-0.web-service`
- `web-1.web-service`

#### Test Connectivity Between Pods:
```bash
kubectl exec web-0 -- ping web-1.web-service
```

---

### 4. Scaling StatefulSets

#### Scale the StatefulSet to Add Pods:
```bash
kubectl scale statefulset web --replicas=5
```

#### Verify the New Pods:
```bash
kubectl get pods -l app=web
```

#### Observe Order:
Check the order in which Pods are added (`web-3`, `web-4`).

---

### 5. Deleting a StatefulSet

Deleting a StatefulSet does not delete its associated PersistentVolumeClaims (PVCs).

#### Delete the StatefulSet:
```bash
kubectl delete statefulset web
```

#### Verify PVCs:
```bash
kubectl get pvc
```

PVCs remain to preserve the data.

---
