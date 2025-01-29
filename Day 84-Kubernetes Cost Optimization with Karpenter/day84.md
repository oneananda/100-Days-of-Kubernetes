---

## Day 84: Kubernetes Cost Optimization with Karpenter  

### 📘 Overview  

Optimizing costs in Kubernetes clusters is crucial for efficient resource utilization. **Karpenter**, an open-source cluster autoscaler, dynamically provisions right-sized compute resources to meet application demands while minimizing costs. This session focuses on installing Karpenter, configuring it for workload-based scaling, and implementing cost-efficient node management strategies.

---


### Objectives  

1. Understand Kubernetes cost optimization challenges and the role of Karpenter.  
2. Install and configure Karpenter for dynamic autoscaling.  
3. Implement best practices for optimizing resource usage and reducing cloud costs.  

---

### Key Concepts  

1. **Kubernetes Cost Optimization:**  
   - Efficiently allocating compute resources to avoid overprovisioning and reduce unnecessary expenses.  

2. **Karpenter:**  
   - A flexible and fast node autoscaler that provisions nodes based on actual pod requirements.  

3. **Dynamic Scaling:**  
   - Automatically adjusting the cluster’s size based on demand to optimize costs.  

4. **Spot Instances & Cost-Efficient Strategies:**  
   - Leveraging spot instances and mixed instance types for cost savings.  

---


### Practical Exercises: Cost Optimization with Karpenter  

---

#### 1. Installing Karpenter  

##### Step 1: Create IAM Role for Karpenter  
For AWS-based clusters, create an IAM role with necessary permissions:  
```bash
aws iam create-role --role-name KarpenterControllerRole --assume-role-policy-document file://karpenter-trust-policy.json
```

Attach necessary policies:  
```bash
aws iam attach-role-policy --role-name KarpenterControllerRole --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess
```

##### Step 2: Deploy Karpenter in Kubernetes  
Add the Karpenter Helm repository:  
```bash
helm repo add karpenter https://charts.karpenter.sh/
helm repo update
```

Install Karpenter using Helm:  
```bash
helm install karpenter karpenter/karpenter --namespace karpenter --create-namespace
```

Verify installation:  
```bash
kubectl get pods -n karpenter
```

---

#### 2. Configuring Karpenter for Dynamic Autoscaling  

##### Step 1: Define a Karpenter Provisioner  
Create a `provisioner.yaml` file:  
```yaml
apiVersion: karpenter.k8s.aws/v1alpha5
kind: Provisioner
metadata:
  name: default
spec:
  requirements:
    - key: "node.kubernetes.io/instance-type"
      operator: In
      values: ["t3.medium", "t3.large"]
  limits:
    resources:
      cpu: "1000"
  provider:
    subnetSelector:
      karpenter.sh/discovery: "my-cluster"
    securityGroupSelector:
      karpenter.sh/discovery: "my-cluster"
  ttlSecondsAfterEmpty: 30
```

Apply the provisioner:  
```bash
kubectl apply -f provisioner.yaml
```

##### Step 2: Configure Workloads for Autoscaling  
Ensure pods have appropriate resource requests:  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: autoscale-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: autoscale-app
  template:
    metadata:
      labels:
        app: autoscale-app
    spec:
      containers:
        - name: app
          image: nginx
          resources:
            requests:
              cpu: "500m"
              memory: "512Mi"
```

Deploy the workload:  
```bash
kubectl apply -f autoscale-app.yaml
```

Karpenter will automatically provision nodes as needed.

---

#### 3. Leveraging Spot Instances for Cost Savings  

##### Step 1: Modify the Provisioner to Use Spot Instances  
Update the provisioner configuration to allow spot instances:  
```yaml
spec:
  provider:
    instanceType: ["t3.medium", "t3.large"]
    spot: true
```

Apply the updated configuration:  
```bash
kubectl apply -f provisioner.yaml
```

##### Step 2: Monitor Autoscaling  
Check if new nodes are being provisioned:  
```bash
kubectl get nodes
```

Check Karpenter logs:  
```bash
kubectl logs -n karpenter -l app.kubernetes.io/name=karpenter
```

---


### Best Practices for Cost Optimization  

1. **Right-Size Nodes:**  
   - Use Karpenter to provision nodes with appropriate CPU/memory resources based on workload needs.  

2. **Utilize Spot Instances:**  
   - Configure Karpenter to use a mix of on-demand and spot instances for cost savings.  

3. **Monitor Resource Utilization:**  
   - Continuously track CPU, memory, and node usage using Prometheus or Grafana.  

4. **Optimize Autoscaling Policies:**  
   - Adjust node lifetimes (`ttlSecondsAfterEmpty`) to minimize unnecessary costs.  

5. **Regularly Review Costs:**  
   - Use cost monitoring tools like Kubecost or AWS Cost Explorer to analyze savings.  

---


### 📝 Document Your Progress  

In your `day84.md` file, include:  
- Steps for installing and configuring Karpenter.  
- Observations on dynamic autoscaling and node provisioning.  
- Insights from leveraging spot instances for cost savings.  

---

### 🎯 Outcome for Day 84  

By the end of this session, you will:  
1. Install and configure Karpenter for Kubernetes autoscaling.  
2. Implement dynamic scaling to optimize resource allocation.  
3. Use spot instances and autoscaling policies for cost savings.  

---

### 🔗 Additional Resources  

- [Karpenter Documentation](https://karpenter.sh/docs/)  
- [AWS Spot Instances](https://aws.amazon.com/ec2/spot/)  
- [Kubernetes Autoscaling Best Practices](https://kubernetes.io/docs/tasks/administer-cluster/cluster-management/)  
- [Kubecost for Cost Optimization](https://kubecost.com/)  

---