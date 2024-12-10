---

## Day 34: Kubernetes StatefulSets — Managing Stateful Applications (Practical Class)

### 📘 Overview

**StatefulSets** in Kubernetes are used to manage stateful applications, ensuring predictable and stable pod identities, persistent storage, and ordered deployment. This practical session focuses on deploying and managing StatefulSets for stateful workloads like databases and distributed systems.

---


### Objectives

1. Understand the role of StatefulSets in Kubernetes.
2. Deploy and manage a StatefulSet with persistent storage.
3. Explore scaling and updates in StatefulSets.
4. Learn best practices for managing stateful workloads.

---

### Key Concepts

1. **Pod Identity**:
   - Pods in a StatefulSet have a unique, stable identity and hostname.

2. **Stable Storage**:
   - Persistent storage is associated with each pod and remains even after the pod is deleted.

3. **Ordered Deployment**:
   - Pods are created, updated, or deleted in a defined order.

4. **Use Cases**:
   - Databases, distributed file systems, and applications requiring stable network identities.

---


### Practical Exercises

---

### 1. Deploying a StatefulSet

#### Define a Headless Service:
Save the following YAML as `headless-service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: stateful-app
  labels:
    app: stateful-app
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: stateful-app
```

#### Apply the Service:
```bash
kubectl apply -f headless-service.yaml
```

---

#### Define a StatefulSet:
Save the following YAML as `statefulset.yaml`:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: stateful-app
spec:
  serviceName: "stateful-app"
  replicas: 3
  selector:
    matchLabels:
      app: stateful-app
  template:
    metadata:
      labels:
        app: stateful-app
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        volumeMounts:
        - name: data
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 1Gi
```

#### Apply the StatefulSet:
```bash
kubectl apply -f statefulset.yaml
```

---

### 2. Exploring StatefulSet Features

#### Verify StatefulSet Pods:
```bash
kubectl get pods -l app=stateful-app
kubectl describe statefulset stateful-app
```

#### Access Pod Hostnames:
```bash
kubectl exec stateful-app-0 -- hostname
kubectl exec stateful-app-1 -- hostname
```

---

### 3. Scaling a StatefulSet

#### Scale the StatefulSet:
```bash
kubectl scale statefulset stateful-app --replicas=5
```

#### Verify the New Pods:
```bash
kubectl get pods -l app=stateful-app
```

---

### 4. Testing Persistence

#### Write Data to a Pod:
1. Exec into a pod:
   ```bash
   kubectl exec -it stateful-app-0 -- /bin/sh
   ```
2. Create a file:
   ```bash
   echo "Hello from stateful-app-0" > /usr/share/nginx/html/index.html
   exit
   ```

#### Delete the Pod:
```bash
kubectl delete pod stateful-app-0
```

#### Verify Data Persistence:
1. Wait for the pod to restart.
2. Exec into the pod and check the file:
   ```bash
   kubectl exec -it stateful-app-0 -- cat /usr/share/nginx/html/index.html
   ```

---

### 5. Rolling Updates in StatefulSets

#### Update the Image:
```bash
kubectl set image statefulset/stateful-app nginx=nginx:1.21
```

#### Observe Ordered Updates:
```bash
kubectl rollout status statefulset stateful-app
kubectl get pods -l app=stateful-app
```

---

### Best Practices for StatefulSets

1. **Use Headless Services**:
   - Ensure predictable network identities for StatefulSet pods.

2. **Stable Storage**:
   - Use `volumeClaimTemplates` to associate persistent storage with pods.

3. **Avoid Over-Scaling**:
   - Scale StatefulSets cautiously to avoid resource contention.

4. **Test Ordered Updates**:
   - Ensure applications handle sequential updates correctly.

5. **Monitor Stateful Workloads**:
   - Use monitoring tools to ensure state consistency and availability.

---

### 📝 Document Your Progress

In your `day34.md` file, record:
- YAML configurations for StatefulSets and services.
- Observations during scaling, persistence, and updates.
- Challenges encountered and troubleshooting steps.

---

### 🎯 Outcome for Day 34

By the end of Day 34, you should:
1. Understand the purpose and behavior of StatefulSets in Kubernetes.
2. Deploy and manage StatefulSets with persistent storage.
3. Perform scaling, updates, and data persistence tests.
4. Apply best practices for managing stateful workloads.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: StatefulSets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)
- [Managing Stateful Applications](https://kubernetes.io/docs/tasks/run-application/run-replicated-stateful-application/)
- [Stateful Workload Patterns](https://kubernetes.io/blog/2020/12/09/kubernetes-patterns-the-statefulset/)

---