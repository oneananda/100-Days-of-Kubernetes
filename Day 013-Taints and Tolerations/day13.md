---

## Day 13: Taints and Tolerations — Managing Pod Scheduling on Nodes

### 📘 Overview

**Taints and Tolerations** are Kubernetes mechanisms to control which Pods can be scheduled on specific nodes. Taints are applied to nodes to repel certain Pods, while Tolerations allow Pods to override these taints.

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

