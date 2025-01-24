---

## Day 79: Kubernetes Cluster Upgrade Strategies

### 📘 Overview

Upgrading a Kubernetes cluster is a critical task to ensure security, stability, and access to the latest features. This session focuses on planning for zero-downtime upgrades, managing upgrades using **kubeadm** and automation tools, and following best practices to maintain cluster availability during the process.

---

### Objectives

1. Plan and execute Kubernetes cluster upgrades with minimal downtime.  
2. Use `kubeadm` and automation tools for seamless upgrades.  
3. Follow best practices to maintain cluster availability and avoid disruptions.  

---

### Key Concepts

1. **Zero-Downtime Upgrades:**  
   - Strategies to minimize or eliminate downtime during cluster upgrades, ensuring high availability.

2. **Upgrade Tools:**  
   - **kubeadm:** A CLI tool for managing Kubernetes cluster upgrades.  
   - **Automation Tools:** Integrating upgrade workflows into CI/CD pipelines using tools like Ansible and Terraform.

3. **Cluster Availability:**  
   - Techniques to ensure workloads remain operational and services are uninterrupted during upgrades.

---


### Practical Exercises: Kubernetes Cluster Upgrade

---

#### 1. Planning for Zero-Downtime Upgrades

##### Step 1: Review Current Cluster State
Check the cluster version and node statuses:
```bash
kubectl version
kubectl get nodes
kubectl get pods --all-namespaces
```

##### Step 2: Assess Upgrade Compatibility
Verify upgrade compatibility using the Kubernetes version skew policy:
- Control plane components must be upgraded before worker nodes.
- kubelet versions cannot be more than one minor version behind the API server.

---

#### 2. Upgrading with kubeadm

##### Step 1: Upgrade the Control Plane
Update the `kubeadm` package:
```bash
sudo apt-get update && sudo apt-get install -y kubeadm=<target-version>
```

Drain the control plane node:
```bash
kubectl drain <control-plane-node> --ignore-daemonsets --delete-local-data
```

Upgrade the control plane components:
```bash
sudo kubeadm upgrade apply <target-version>
```

Uncordon the node:
```bash
kubectl uncordon <control-plane-node>
```

##### Step 2: Upgrade Worker Nodes
Repeat for each worker node:
1. Drain the node:
   ```bash
   kubectl drain <worker-node> --ignore-daemonsets --delete-local-data
   ```
2. Upgrade kubelet and kubectl:
   ```bash
   sudo apt-get update && sudo apt-get install -y kubelet=<target-version> kubectl=<target-version>
   ```
3. Restart kubelet:
   ```bash
   sudo systemctl restart kubelet
   ```
4. Uncordon the node:
   ```bash
   kubectl uncordon <worker-node>
   ```

---

#### 3. Using Automation Tools for Upgrades

##### Step 1: Automate Upgrades with Ansible
Create a playbook to upgrade nodes in a rolling fashion:
```yaml
- name: Upgrade Kubernetes Nodes
  hosts: all
  tasks:
    - name: Drain Node
      command: kubectl drain {{ inventory_hostname }} --ignore-daemonsets --delete-local-data
    - name: Upgrade kubelet and kubectl
      apt:
        name: "{{ item }}"
        state: latest
      with_items:
        - kubelet
        - kubectl
    - name: Restart kubelet
      service:
        name: kubelet
        state: restarted
    - name: Uncordon Node
      command: kubectl uncordon {{ inventory_hostname }}
```

Run the playbook:
```bash
ansible-playbook -i inventory upgrade-playbook.yml
```

##### Step 2: Integrate Upgrades into CI/CD Pipelines
Use tools like Jenkins or GitHub Actions to trigger upgrades automatically on new version releases.

---