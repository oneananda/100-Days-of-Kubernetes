---

## Day 51: Kubernetes Stateful Applications with Operators — Practical Class

### 📘 Overview

While Operators are powerful for managing Kubernetes workloads, they shine when applied to **stateful applications** like databases or distributed systems. Stateful applications require specific handling for persistence, ordering, and scaling, which can be effectively managed with a Kubernetes Operator.

This session focuses on deploying a stateful application (e.g., MySQL) using a custom Operator to manage its lifecycle.

---

### Objectives

1. Understand the challenges of running stateful applications in Kubernetes.
2. Deploy a StatefulSet for persistence and ordered deployment.
3. Extend StatefulSet functionality with a custom Operator.
4. Manage the stateful application lifecycle with the Operator.

---

### Key Concepts

1. **Stateful Applications**:
   - Require persistent storage and consistent networking.
   - Need ordered startup, scaling, and termination.

2. **StatefulSets**:
   - Kubernetes resource designed for managing stateful applications.
   - Ensures predictable pod identities and persistent storage.

3. **Operators for Stateful Applications**:
   - Automate tasks like scaling, backup, and recovery for stateful workloads.
   - Provide domain-specific management logic.

---

### Practical Exercises

---

### 1. Deploying a StatefulSet for MySQL

#### Step 1: Create a ConfigMap for MySQL Configuration
Create a `mysql-configmap.yaml` file:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-config
data:
  my.cnf: |
    [mysqld]
    sql_mode=NO_ENGINE_SUBSTITUTION
```

Apply the ConfigMap:
```bash
kubectl apply -f mysql-configmap.yaml
```

---

#### Step 2: Deploy a StatefulSet
Create a `mysql-statefulset.yaml` file:
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: "mysql"
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: rootpassword
        volumeMounts:
        - name: mysql-data
          mountPath: /var/lib/mysql
        - name: mysql-config
          mountPath: /etc/mysql/conf.d
      volumes:
      - name: mysql-config
        configMap:
          name: mysql-config
  volumeClaimTemplates:
  - metadata:
      name: mysql-data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 5Gi
```

Apply the StatefulSet:
```bash
kubectl apply -f mysql-statefulset.yaml
```

Verify the StatefulSet:
```bash
kubectl get statefulsets
kubectl get pods -l app=mysql
```

---

### 2. Extending StatefulSet with an Operator

#### Step 1: Create a CRD for MySQL
Define a `mysql-crd.yaml` file:
```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: mysqlclusters.example.com
spec:
  group: example.com
  names:
    plural: mysqlclusters
    singular: mysqlcluster
    kind: MySQLCluster
  scope: Namespaced
  versions:
  - name: v1
    served: true
    storage: true
    schema:
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            properties:
              replicas:
                type: integer
              rootPassword:
                type: string
```

Apply the CRD:
```bash
kubectl apply -f mysql-crd.yaml
```

---

#### Step 2: Create a Custom Resource
Define a `mysql-resource.yaml` file:
```yaml
apiVersion: example.com/v1
kind: MySQLCluster
metadata:
  name: mysql-cluster
spec:
  replicas: 3
  rootPassword: rootpassword
```

Apply the custom resource:
```bash
kubectl apply -f mysql-resource.yaml
```

Verify the custom resource:
```bash
kubectl get mysqlclusters
```

---

#### Step 3: Deploy a Simple Operator
Create a `mysql-operator.yaml` file with logic to reconcile the custom resource:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-operator
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql-operator
  template:
    metadata:
      labels:
        app: mysql-operator
    spec:
      containers:
      - name: operator
        image: busybox
        command:
          - sh
          - -c
          - |
            while true; do
              echo "Reconciling MySQL Cluster CR";
              # Simulate reconciling logic for StatefulSet updates
              sleep 10;
            done
```

Apply the Operator:
```bash
kubectl apply -f mysql-operator.yaml
```

---

### 3. Testing and Managing the Stateful Application

#### Step 1: Scale the Cluster
Update `mysql-resource.yaml` to scale the replicas:
```yaml
spec:
  replicas: 5
```

Apply the updated resource:
```bash
kubectl apply -f mysql-resource.yaml
```

Monitor the Operator logs:
```bash
kubectl logs -l app=mysql-operator
```

Verify the StatefulSet scaling:
```bash
kubectl get statefulsets
kubectl get pods -l app=mysql
```

---

### 4. Cleanup

Remove all resources:
```bash
kubectl delete -f mysql-resource.yaml
kubectl delete -f mysql-operator.yaml
kubectl delete -f mysql-crd.yaml
kubectl delete -f mysql-statefulset.yaml
kubectl delete -f mysql-configmap.yaml
```

---
