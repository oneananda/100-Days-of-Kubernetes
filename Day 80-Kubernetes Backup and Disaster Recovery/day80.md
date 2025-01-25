---

## Day 80: Kubernetes Backup and Disaster Recovery

### 📘 Overview

Backup and disaster recovery are crucial components of Kubernetes cluster management to ensure data safety and business continuity. Tools like **Velero** and **Stash** simplify automating backups and restoring applications and data after failures. This session focuses on understanding these tools, automating backups for critical workloads, and restoring clusters and applications during disaster recovery scenarios.

---


### Objectives

1. Explore tools like Velero and Stash for Kubernetes backup and disaster recovery.  
2. Automate backups for critical workloads in a Kubernetes cluster.  
3. Practice restoring applications and data to recover from failures.  

---

### Key Concepts

1. **Backup and Disaster Recovery (DR):**  
   - Processes to safeguard cluster data and ensure quick recovery from disruptions.  

2. **Velero:**  
   - An open-source tool for backups, disaster recovery, and migrations in Kubernetes.  

3. **Stash:**  
   - A Kubernetes-native backup solution to create and restore application data snapshots.  

4. **Backup Strategies:**  
   - Full, incremental, and differential backups for cluster data and application workloads.

---


### Practical Exercises: Kubernetes Backup and Disaster Recovery

---

#### 1. Setting Up Velero for Backups

##### Step 1: Install Velero
Install Velero CLI and configure a cloud storage provider (e.g., AWS S3):
```bash
velero install \
    --provider aws \
    --plugins velero/velero-plugin-for-aws:v1.6.0 \
    --bucket <your-bucket-name> \
    --secret-file ./credentials-velero \
    --backup-location-config region=<region>
```

Verify Velero installation:
```bash
kubectl get pods -n velero
```

##### Step 2: Create a Backup
Create a backup for all namespaces:
```bash
velero backup create cluster-backup --include-namespaces *
```

List backups to ensure it was created:
```bash
velero backup get
```

##### Step 3: Schedule Automated Backups
Schedule periodic backups:
```bash
velero create schedule daily-backup --schedule="@every 24h" --include-namespaces *
```

---

#### 2. Setting Up Stash for Application Backups

##### Step 1: Deploy Stash
Install Stash using Helm:
```bash
helm repo add appscode https://charts.appscode.com/stable/
helm repo update
helm install stash appscode/stash --namespace kube-system
```

Verify installation:
```bash
kubectl get pods -n kube-system -l app=stash
```

##### Step 2: Backup Application Data
Annotate a Deployment to enable backups:
```bash
kubectl annotate deployment <deployment-name> stash.appscode.com/backup-template=demo-backup
```

Create a `Repository` resource for storing backups:
```yaml
apiVersion: stash.appscode.com/v1alpha1
kind: Repository
metadata:
  name: demo-repo
  namespace: default
spec:
  backend:
    s3:
      endpoint: s3.amazonaws.com
      bucket: <your-bucket-name>
      prefix: stash-backups
    storageSecretName: s3-secret
```

Apply the configuration:
```bash
kubectl apply -f repository.yaml
```

---

#### 3. Restoring Applications and Data

##### Step 1: Restore with Velero
Restore from a backup:
```bash
velero restore create --from-backup cluster-backup
```

Verify the restoration:
```bash
kubectl get pods --all-namespaces
```

##### Step 2: Restore with Stash
Create a `RestoreSession` resource:
```yaml
apiVersion: stash.appscode.com/v1alpha1
kind: RestoreSession
metadata:
  name: demo-restore
  namespace: default
spec:
  repository:
    name: demo-repo
  target:
    ref:
      apiVersion: apps/v1
      kind: Deployment
      name: <deployment-name>
```

Apply the configuration:
```bash
kubectl apply -f restore-session.yaml
```

---


### Best Practices for Backup and Disaster Recovery

1. **Automate Regular Backups:**  
   - Schedule automated backups using Velero or Stash to minimize manual intervention.  

2. **Test Restorations Regularly:**  
   - Periodically validate backups by performing test restorations in a staging environment.  

3. **Store Backups Off-Site:**  
   - Use cloud storage solutions to ensure backups are safe from on-premises disasters.  

4. **Implement Granular Backups:**  
   - Backup critical namespaces, Persistent Volumes (PVs), and configurations selectively to save storage space.  

5. **Document the Recovery Plan:**  
   - Maintain a step-by-step disaster recovery guide for your team.

---


### 📝 Document Your Progress

In your `day80.md` file, include:  
- Steps for installing and using Velero and Stash.  
- Configuration details for backup and restoration processes.  
- Insights from automating backups and testing disaster recovery scenarios.  

---

### 🎯 Outcome for Day 80

By the end of this session, you will:  
1. Set up Velero and Stash for Kubernetes backup and disaster recovery.  
2. Automate backups for critical workloads in your cluster.  
3. Perform application and data restoration to recover from failures.  

---

### 🔗 Additional Resources

- [Velero Documentation](https://velero.io/docs/)  
- [Stash Documentation](https://stash.appscode.com/)  
- [Kubernetes Backup and Restore](https://kubernetes.io/docs/tasks/administer-cluster/safeguard-cluster/)  
- [Cloud Backup Best Practices](https://aws.amazon.com/backup/)  

---