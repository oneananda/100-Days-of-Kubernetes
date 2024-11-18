---

## Day 11: Working with Namespaces — Organizing and Isolating Cluster Resources

### 📘 Overview

**Namespaces** in Kubernetes provide a way to divide cluster resources between multiple users or projects. They are useful for isolating environments (e.g., development, staging, production) and organizing resources for better management.

---

### What is a Namespace?

- A Namespace is a virtual cluster within a Kubernetes cluster.
- Namespaces allow multiple teams or applications to share a cluster without interfering with each other.
- Resources like Pods, Services, and Deployments can be assigned to specific Namespaces, and their scope is limited to that Namespace.

---

### Key Features of Namespaces

1. **Resource Isolation**: Separate resources for different environments or teams.
2. **Resource Quotas**: Limit the resources (CPU, memory, etc.) used by a Namespace.
3. **Access Control**: Restrict access to resources within a Namespace using RBAC (Role-Based Access Control).
4. **Default Namespace**: If no Namespace is specified, resources are created in the `default` Namespace.

---

### Hands-On with Namespaces

Let’s explore creating, listing, and managing Namespaces and their resources.

---

### 1. Viewing and Using Default Namespaces

Every Kubernetes cluster includes these default Namespaces:

- `default`: The default Namespace for resources when no Namespace is specified.
- `kube-system`: Contains system components like the API server, scheduler, and DNS.
- `kube-public`: Readable by all users, often used for public information.
- `kube-node-lease`: Manages node leases for node heartbeat tracking.

#### List All Namespaces
```bash
kubectl get namespaces
```

#### View Resources in a Namespace
To list resources (e.g., Pods) in a specific Namespace:
```bash
kubectl get pods -n kube-system
```

---


### 2. Creating a New Namespace

#### Create a Namespace Using `kubectl`
```bash
kubectl create namespace dev
```

#### Create a Namespace Using a YAML File

Create a file named `dev-namespace.yaml`:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

Apply the file:
```bash
kubectl apply -f dev-namespace.yaml
```

#### Verify the Namespace
```bash
kubectl get namespaces
```

---


### 3. Using a Namespace for Resources

By default, resources are created in the `default` Namespace. You can specify a Namespace explicitly.

#### Create a Deployment in a Specific Namespace
Use the `-n` flag to specify the Namespace:
```bash
kubectl create deployment nginx --image=nginx -n dev
```

#### List Resources in a Namespace
```bash
kubectl get all -n dev
```

#### Set a Default Namespace for `kubectl`
To avoid typing `-n <namespace>` repeatedly, set a default Namespace for your `kubectl` context:
```bash
kubectl config set-context --current --namespace=dev
```

---

### 4. Managing Namespaces

#### Delete a Namespace
Deleting a Namespace removes all resources within it:
```bash
kubectl delete namespace dev
```

#### Annotate a Namespace
You can add metadata to a Namespace using annotations:
```bash
kubectl annotate namespace dev purpose=development
```

---

### 5. Using Resource Quotas with Namespaces

Resource quotas limit the amount of CPU, memory, or other resources that a Namespace can use.

#### Create a Resource Quota

Create a file named `resource-quota.yaml`:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
```

Apply the resource quota:
```bash
kubectl apply -f resource-quota.yaml
```

#### Verify the Quota
```bash
kubectl get resourcequota -n dev
```

---


### 6. Access Control with Namespaces

You can use Role-Based Access Control (RBAC) to restrict access to specific Namespaces. For example, create a Role and RoleBinding to allow a user to manage resources in the `dev` Namespace.

To implement **Access Control with Namespaces** in Kubernetes using Role-Based Access Control (RBAC), follow these steps:


### **Step 1: Create a Namespace**
Ensure the Namespace (`dev` in this case) exists.

```bash
kubectl create namespace dev
```


### **Step 2: Define a Role**
A Role is used to grant permissions for specific resources within a Namespace. Create a YAML file named `dev-role.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: dev-role
rules:
  - apiGroups: [""] # "" indicates the core API group
    resources: ["pods", "services", "configmaps"]
    verbs: ["create", "get", "list", "update", "delete"]
  - apiGroups: ["apps"]
    resources: ["deployments"]
    verbs: ["create", "get", "list", "update", "delete"]
```

Apply the Role:

```bash
kubectl apply -f dev-role.yaml
```


### **Step 3: Create a RoleBinding**
A RoleBinding associates a Role with a specific user, group, or service account. Create a YAML file named `dev-rolebinding.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-rolebinding
  namespace: dev
subjects:
  - kind: User
    name: your-username # Replace with the actual username
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: dev-role
  apiGroup: rbac.authorization.k8s.io
```

Apply the RoleBinding:

```bash
kubectl apply -f dev-rolebinding.yaml
```


### **Step 4: Verify Access**
To test the access restrictions:

1. Authenticate as the specified user (`your-username`).
2. Try accessing resources in the `dev` Namespace.

Example command for listing pods:

```bash
kubectl get pods -n dev
```

If configured correctly, the user will have access to the specified resources in the `dev` Namespace but not in other Namespaces.


### **Notes:**
- Replace `your-username` with the user or group you want to grant access to.
- Use `ClusterRole` and `ClusterRoleBinding` if permissions span across all Namespaces.
- For authentication, Kubernetes may require an external identity provider (e.g., certificates, IAM).

---

### Hands-On Practice

1. **Create Namespaces**: Create a `dev` and `prod` Namespace using `kubectl` and YAML.
2. **Deploy Resources**: Create resources in each Namespace and verify their isolation.
3. **Set Resource Quotas**: Apply a Resource Quota to limit resource usage in the `dev` Namespace.
4. **Test Access**: Use `kubectl` to switch between Namespaces and observe resource segregation.

---

### 📝 Document Your Progress

In your `day11.md` file, record:
- Commands and YAML configurations used to create and manage Namespaces.
- Observations about how resources are organized and isolated.
- Notes on any challenges encountered or insights gained.

---

### 🎯 Outcome for Day 11

By the end of Day 11, you should:
1. Understand the purpose and functionality of Namespaces in Kubernetes.
2. Know how to create and manage Namespaces and their resources.
3. Be able to apply resource quotas and manage resource isolation effectively.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
- [Resource Quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/)

---

