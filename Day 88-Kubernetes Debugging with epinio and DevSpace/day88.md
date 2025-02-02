---

## Day 88: Kubernetes Debugging with epinio and DevSpace

### 📘 Overview

In this session, you'll explore how to accelerate your development cycles by combining **epinio** and **DevSpace**. Epinio simplifies application deployment on Kubernetes, allowing for rapid iterations, while DevSpace empowers you to debug live applications with ease. Together, these tools streamline local Kubernetes development workflows, enabling you to quickly develop, test, and debug your applications in a real Kubernetes environment.

---


### Objectives

1. **Rapid Deployment:** Learn to use epinio for fast and efficient application deployment on Kubernetes.
2. **Live Debugging:** Leverage DevSpace to debug and monitor live applications running in your cluster.
3. **Streamlined Workflows:** Simplify local Kubernetes development by integrating deployment and debugging tools into a seamless workflow.

---

### Key Concepts

1. **Epinio:**  
   - A platform-as-a-service (PaaS) tool designed to abstract away the complexities of Kubernetes deployment.
   - Enables developers to push code directly to Kubernetes without managing intricate configuration details.

2. **DevSpace:**  
   - A development and debugging tool that allows you to work on live applications in Kubernetes.
   - Supports hot reloading, real-time log streaming, and remote debugging to accelerate the development process.

3. **Local Kubernetes Development:**  
   - Combines rapid deployment and live debugging to create an efficient iterative development process.
   - Helps bridge the gap between local code changes and their impact on running applications in Kubernetes.

---


### Practical Exercises: Debugging with epinio and DevSpace

#### 1. Setting Up and Using epinio

##### Step 1: Install the epinio CLI  
Download and install the epinio CLI to interact with your Kubernetes cluster:
```bash
curl -LO https://github.com/epinio/epinio/releases/latest/download/epinio-linux-amd64.tar.gz
tar -xzf epinio-linux-amd64.tar.gz
sudo mv epinio /usr/local/bin/
```

##### Step 2: Deploy Your Application with epinio  
Log in to your epinio server and deploy your application:
```bash
epinio login https://<your-epinio-server>
epinio push my-app --path ./my-app-directory
```
Monitor the deployment process until your application is live on Kubernetes.

---

#### 2. Debugging Live Applications with DevSpace

##### Step 1: Install DevSpace  
Install the DevSpace CLI tool:
```bash
curl -s -L https://downloads.devspace.sh/latest/developers | bash
```

##### Step 2: Initialize DevSpace in Your Project  
In your application’s root directory, run:
```bash
devspace init
```
This creates a `devspace.yaml` configuration file tailored to your project.

##### Step 3: Start a Development Session  
Launch DevSpace in development mode to sync code changes and attach debugging sessions:
```bash
devspace dev
```
This command will:
- Sync your local code changes with the container running in Kubernetes.
- Stream real-time logs to help you monitor application behavior.
- Allow you to set breakpoints and debug issues on the fly.

---

#### 3. Simplifying Local Kubernetes Development Workflows

##### Step 1: Enable Hot Reloading  
With DevSpace, any changes you make to your source code are automatically synchronized to the running application. Try modifying your code locally and observe how the changes are reflected almost immediately in your Kubernetes deployment.

##### Step 2: Real-Time Log Streaming  
Use DevSpace’s log streaming capabilities to monitor live application logs:
```bash
devspace logs
```
This helps in quickly identifying and diagnosing issues as they occur in your live environment.

---