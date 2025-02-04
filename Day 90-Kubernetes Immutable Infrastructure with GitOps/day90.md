---

## Day 90: Kubernetes Immutable Infrastructure with GitOps

### 📘 Overview

In this session, you'll explore how GitOps transforms Kubernetes infrastructure management by treating configurations as immutable code. Leveraging tools like **ArgoCD** and **Flux**, you can automate deployment pipelines, ensure consistency across environments, and maintain a clear audit trail of every change. This session covers implementing GitOps workflows, managing Kubernetes configurations as code, and adopting best practices for CI/CD pipelines in GitOps-driven environments.

---


### Objectives

1. **Implement GitOps with ArgoCD and Flux:**  
   - Learn how to set up and configure GitOps tools to automate and manage Kubernetes deployments.

2. **Manage Configurations as Code:**  
   - Treat infrastructure and application configurations as version-controlled code for consistency and auditability.

3. **Optimize CI/CD Pipelines:**  
   - Integrate best practices in CI/CD workflows to enhance the reliability and repeatability of deployments in a GitOps framework.

---

### Key Concepts

1. **GitOps:**  
   - A methodology that uses Git repositories as the single source of truth for declarative infrastructure and application configurations.
   - Enables automated synchronization of desired state with the actual state in Kubernetes clusters.

2. **Immutable Infrastructure:**  
   - Once deployed, infrastructure components (e.g., container images, Kubernetes manifests) are not altered. Instead, any change is applied by replacing the component.
   - Enhances reliability, consistency, and traceability.

3. **GitOps Tools:**  
   - **ArgoCD:** A declarative, GitOps continuous delivery tool for Kubernetes that automates deployment and synchronization of Kubernetes manifests.
   - **Flux:** A set of continuous and progressive delivery solutions for Kubernetes that ensure cluster configurations are always up-to-date with a Git repository.

4. **CI/CD Pipelines in GitOps:**  
   - Automated pipelines that build, test, and deploy code changes, while automatically updating Git repositories with the desired state for your cluster.

---


### Practical Exercises: Implementing GitOps Workflows

---

#### 1. Setting Up GitOps with ArgoCD and Flux

##### Step 1: Install ArgoCD

Deploy ArgoCD in your Kubernetes cluster:
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Access the ArgoCD UI by port-forwarding:
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

##### Step 2: Install Flux

Deploy Flux using its CLI:
```bash
curl -s https://fluxcd.io/install.sh | sudo bash
flux install --namespace=flux-system
```

Verify the installation:
```bash
kubectl get pods -n flux-system
```

---

#### 2. Managing Kubernetes Configurations as Code

##### Step 1: Create a Git Repository for Configurations

Set up a Git repository that will store all Kubernetes manifests (e.g., deployments, services, config maps).

##### Step 2: Connect ArgoCD/Flux to Your Repository

For ArgoCD, create an application that syncs with your repository:
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: 'https://github.com/your-org/your-config-repo.git'
    targetRevision: HEAD
    path: manifests/my-app
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

Apply the application manifest:
```bash
kubectl apply -f my-app-application.yaml -n argocd
```

For Flux, configure GitRepository and Kustomization resources:
```yaml
apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: GitRepository
metadata:
  name: my-configs
  namespace: flux-system
spec:
  interval: 1m0s
  url: https://github.com/your-org/your-config-repo.git
  branch: main
```

```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: my-app
  namespace: flux-system
spec:
  interval: 10m0s
  path: "./manifests/my-app"
  prune: true
  sourceRef:
    kind: GitRepository
    name: my-configs
```

Apply these manifests:
```bash
kubectl apply -f gitrepository.yaml -n flux-system
kubectl apply -f kustomization.yaml -n flux-system
```

---

#### 3. Best Practices for CI/CD Pipelines in GitOps Workflows

##### Step 1: Automate Pipeline Stages

- **Build:** Use CI tools (e.g., GitHub Actions, Jenkins) to build container images upon code commits.
- **Test:** Integrate automated testing (unit, integration, security tests) before deployment.
- **Deploy:** Automatically update Kubernetes manifests in your Git repository, triggering GitOps tools to sync changes.

##### Step 2: Secure Your Pipeline

- Implement branch protection rules.
- Use signed commits and image signing to ensure integrity.
- Monitor pipeline execution with logging and alerts.

##### Step 3: Monitor and Roll Back

- Leverage ArgoCD/Flux monitoring dashboards to track synchronization status.
- Use Git history to roll back to previous, stable configurations in case of failures.

---


### Best Practices for Immutable Infrastructure with GitOps

1. **Maintain a Single Source of Truth:**  
   - Use Git repositories to store all configuration and infrastructure code.

2. **Automate Synchronization:**  
   - Configure continuous reconciliation between Git and your cluster to ensure consistency.

3. **Version Control Everything:**  
   - Use semantic versioning and pull requests for all changes to your configurations.

4. **Implement Robust CI/CD Pipelines:**  
   - Automate build, test, and deploy processes to reduce manual errors and accelerate delivery.

5. **Monitor and Audit Changes:**  
   - Regularly review synchronization logs and Git commit history for auditing and troubleshooting.

---


### 📝 Document Your Progress

In your `day90.md` file, include:
- Steps for installing and configuring ArgoCD and Flux.
- Repository structure and sample configuration manifests.
- CI/CD pipeline configurations and screenshots demonstrating automated deployments.
- Observations on how GitOps improves infrastructure consistency and reliability.

---

### 🎯 Outcome for Day 90

By the end of this session, you will:
1. Implement GitOps workflows using ArgoCD and Flux to manage Kubernetes infrastructure.
2. Manage Kubernetes configurations as immutable code stored in Git.
3. Integrate best practices into your CI/CD pipelines for reliable, automated deployments.

---

### 🔗 Additional Resources

- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Flux Documentation](https://fluxcd.io/docs/)
- [GitOps Principles](https://www.weave.works/technologies/gitops/)
- [Kubernetes Best Practices](https://kubernetes.io/docs/setup/best-practices/)
- [CI/CD with GitOps](https://docs.microsoft.com/en-us/azure/devops/learn/gitops/)

---