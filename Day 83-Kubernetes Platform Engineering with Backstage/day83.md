---

## Day 83: Kubernetes Platform Engineering with Backstage

### 📘 Overview

Backstage is an open-source platform for building developer portals, enabling teams to streamline workflows and manage internal tools effectively. With its Kubernetes integration, Backstage empowers developers to perform self-service deployments, access internal tools, and monitor services efficiently. This session focuses on setting up Backstage, integrating it with Kubernetes, and leveraging it for platform engineering.

---


### Objectives

1. Understand Backstage as a developer portal and its role in platform engineering.  
2. Set up and integrate Backstage with Kubernetes for self-service deployments.  
3. Efficiently manage internal tools and services using Backstage plugins.  

---

### Key Concepts

1. **Backstage:**  
   - An open-source developer portal platform created by Spotify, used for managing services, APIs, documentation, and deployments.  

2. **Self-Service Deployment:**  
   - Allowing developers to deploy and manage applications directly without needing extensive operational support.  

3. **Platform Engineering:**  
   - Building internal platforms and tools that improve developer experience and productivity.  

---


### Practical Exercises: Kubernetes Platform Engineering with Backstage

---

#### 1. Setting Up Backstage

##### Step 1: Install Backstage Locally
Create a new Backstage application:
```bash
npx @backstage/create-app@latest
cd <your-app-name>
```

Run the application locally:
```bash
yarn dev
```

Access the Backstage UI at [http://localhost:3000](http://localhost:3000).

##### Step 2: Deploy Backstage in Kubernetes
Build a Docker image for Backstage:
```bash
docker build -t <your-dockerhub-username>/backstage .
docker push <your-dockerhub-username>/backstage
```

Create Kubernetes manifests for Backstage:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backstage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: backstage
  template:
    metadata:
      labels:
        app: backstage
    spec:
      containers:
        - name: backstage
          image: <your-dockerhub-username>/backstage
          ports:
            - containerPort: 3000
```

Apply the manifests:
```bash
kubectl apply -f backstage-deployment.yaml
```

Expose Backstage with a Service:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: backstage
spec:
  type: NodePort
  ports:
    - port: 3000
      targetPort: 3000
  selector:
    app: backstage
```

Access Backstage using the NodePort URL.

---

#### 2. Integrating Backstage with Kubernetes

##### Step 1: Install the Kubernetes Plugin
Add the Kubernetes plugin to Backstage:
```bash
yarn add @backstage/plugin-kubernetes
```

Configure the Kubernetes plugin in `app-config.yaml`:
```yaml
kubernetes:
  clusterLocatorMethods:
    - type: config
      clusters:
        - name: my-cluster
          url: https://<kubernetes-api-server>
          authProvider: serviceAccount
```

Provide the service account credentials for Backstage to access the cluster.

##### Step 2: Enable Self-Service Deployment
Add a Kubernetes resource card to Backstage:
1. Navigate to your service in the Backstage UI.  
2. Link deployment manifests (e.g., Helm charts) to the service.  
3. Allow developers to deploy directly from Backstage.

---

#### 3. Managing Internal Tools and Services

##### Step 1: Register Services in Backstage
Create a `catalog-info.yaml` file for each service:
```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: my-service
  description: My Kubernetes service
spec:
  type: service
  lifecycle: production
  owner: team-name
```

Add the service to the catalog:
```bash
kubectl apply -f catalog-info.yaml
```

View the service in the Backstage catalog.

##### Step 2: Leverage Backstage Plugins
Install additional plugins for monitoring, CI/CD, or API documentation:
- **Monitoring:** Add Grafana or Prometheus plugins.  
- **CI/CD:** Integrate Jenkins or GitHub Actions plugins.  
- **Documentation:** Use the TechDocs plugin for auto-generated documentation.  

---