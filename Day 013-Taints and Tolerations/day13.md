---

## Day 13: Taints and Tolerations — Managing Pod Scheduling on Nodes

### 📘 Overview

**Taints and Tolerations** are Kubernetes mechanisms to control which Pods can be scheduled on specific nodes. Taints are applied to nodes to repel certain Pods, while Tolerations allow Pods to override these taints.

---

### What are Taints and Tolerations?

1. **Taints**:  
   - Applied to nodes to prevent Pods from being scheduled unless they tolerate the taint.
   - Format: `key=value:effect`
   - **Effects**:
     - `NoSchedule`: Prevents scheduling of Pods without tolerations.
     - `PreferNoSchedule`: Avoids scheduling Pods if possible.
     - `NoExecute`: Evicts Pods that don’t tolerate the taint.

2. **Tolerations**:  
   - Added to Pods to allow them to be scheduled on tainted nodes.
   - Format: Defined in the Pod’s YAML to match the taint’s key, value, and effect.

---

### **Taints and Tolerations Made Simple**

- **Taint**: A **rule** on a Node that says,  
  *"Only specific Pods are allowed here."*  
  Example: *"This Node is reserved for special workloads."*

- **Toleration**: A **pass** that lets a Pod **bypass the rule** (taint) and run on the Node.  
  Example: *"This Pod is allowed to use the special Node."*

---

### **Think of it like this:**
- **Taint**: A "No Entry" sign on a door.  
- **Toleration**: A "Permission Slip" that allows certain people to enter despite the sign.

---

### **How They Work Together**
1. **Without a Toleration**: A Pod cannot enter (run on) a tainted Node.
2. **With a Toleration**: A Pod is allowed to bypass the "No Entry" rule and run on the Node.

---

### **Key Effects**
- **NoSchedule**: Pods without the pass (toleration) won't be scheduled.
- **PreferNoSchedule**: Kubernetes tries to avoid scheduling Pods here, but may allow it if necessary.
- **NoExecute**: Pods without the pass will be **evicted** from the Node and can't enter again.

---

### Taints and Tolerations - Key Features

1. **Node Protection**: Reserve nodes for specific workloads or environments.
2. **Fault Tolerance**: Evict Pods if a node becomes unsuitable.
3. **Workload Segregation**: Ensure certain types of workloads only run on specific nodes.

---

### Hands-On with Taints and Tolerations

Let’s explore applying taints to nodes and configuring Pods with tolerations.

---

### 1. Adding a Taint to a Node

#### Add a Taint
```bash
kubectl taint nodes <node-name> key=value:NoSchedule
```

Example:
```bash
kubectl taint nodes node1 environment=production:NoSchedule
```

#### Verify the Taint
```bash
kubectl describe node <node-name>
```

---

### 2. Scheduling Pods with Tolerations

#### Create a Pod with a Matching Toleration

Create a YAML file named `toleration-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: toleration-pod
spec:
  tolerations:
    - key: "environment"
      operator: "Equal"
      value: "production"
      effect: "NoSchedule"
  containers:
    - name: nginx
      image: nginx
```

#### Apply the Pod Configuration:
```bash
kubectl apply -f toleration-pod.yaml
```

#### Verify Pod Scheduling:
```bash
kubectl get pods -o wide
```

---

### 3. Removing a Taint from a Node

If you no longer need the taint, you can remove it.

#### Remove a Taint:
```bash
kubectl taint nodes <node-name> key=value:NoSchedule-
```

Example:
```bash
kubectl taint nodes node1 environment=production:NoSchedule-
```

---


### 4. Taints with `NoExecute` Effect

The `NoExecute` effect evicts existing Pods that do not tolerate the taint.

#### Add a Taint with `NoExecute`:
```bash
kubectl taint nodes <node-name> key=value:NoExecute
```

#### Example Pod with Toleration for `NoExecute`:
```yaml
tolerations:
  - key: "environment"
    operator: "Equal"
    value: "production"
    effect: "NoExecute"
    tolerationSeconds: 600
```

This configuration allows the Pod to remain on the node for 600 seconds after the taint is applied.

---


### 📝 Document Your Progress

In your `day13.md` file, record:
- Commands and YAML configurations used for taints and tolerations.
- Observations about how Pods are scheduled or evicted based on taints.
- Notes on any challenges encountered or insights gained.

---

### 🎯 Outcome for Day 13

By the end of Day 13, you should:
1. Understand how taints and tolerations control Pod scheduling.
2. Be able to configure taints on nodes and tolerations in Pod specifications.
3. Use `NoSchedule`, `PreferNoSchedule`, and `NoExecute` effects effectively.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)
- [Node Affinity and Taints](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)

---