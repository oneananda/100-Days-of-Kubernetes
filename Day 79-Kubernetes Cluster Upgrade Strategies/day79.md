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


### Best Practices for Cluster Upgrades

1. **Backup Before Upgrading:**  
   - Backup etcd data and cluster state. Example:
     ```bash
     ETCDCTL_API=3 etcdctl snapshot save snapshot.db
     ```

2. **Use Staged Rollouts:**  
   - Upgrade control plane nodes first, followed by worker nodes.

3. **Monitor During Upgrades:**  
   - Use monitoring tools like Prometheus and Grafana to track cluster health.

4. **Test in Staging First:**  
   - Always test upgrades in a staging environment before applying them to production.

5. **Review Application Readiness:**  
   - Ensure applications have proper readiness and liveness probes.

---


### 📝 Document Your Progress

In your `day79.md` file, include:  
- Steps for planning and executing zero-downtime upgrades.  
- Commands and tools used for the upgrade process.  
- Challenges faced and solutions applied.  

---

### 🎯 Outcome for Day 79

By the end of this session, you will:  
1. Plan and execute zero-downtime upgrades for Kubernetes clusters.  
2. Upgrade a cluster using kubeadm and automation tools like Ansible.  
3. Follow best practices to ensure high availability during upgrades.  

---

### 🔗 Additional Resources

- [Kubernetes Upgrading a Cluster](https://kubernetes.io/docs/tasks/administer-cluster/cluster-upgrade/)  
- [kubeadm Documentation](https://kubernetes.io/docs/reference/setup-tools/kubeadm/)  
- [Kubernetes Version Skew Policy](https://kubernetes.io/releases/version-skew-policy/)  
- [Ansible Playbooks for Kubernetes](https://docs.ansible.com/)  

--- 