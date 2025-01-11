---

## Day 66: Kubernetes Advanced Scheduling — Custom Schedulers and Extensibility

### 📘 Overview

Kubernetes provides a default scheduler, but some workloads may require customized scheduling logic. Kubernetes allows the use of **custom schedulers** and extensions to tailor pod placement based on specific business or technical requirements. This session focuses on creating and deploying custom schedulers and leveraging the Kubernetes Scheduling Framework for extensibility.

---

### Objectives

1. Understand the role of custom schedulers in Kubernetes.
2. Learn how to configure and use a custom scheduler.
3. Explore the Kubernetes Scheduling Framework for advanced extensibility.

---

### Key Concepts

1. **Custom Schedulers**:
   - Separate from the default Kubernetes scheduler.
   - Used to implement unique scheduling logic for specific workloads.

2. **Kubernetes Scheduling Framework**:
   - Provides hooks and plugins for extending the scheduling process.
   - Supports features like custom scoring, filtering, and preemption logic.

3. **Use Cases**:
   - Workloads with strict placement constraints or dependencies.
   - Custom scoring algorithms for resource optimization.

---

### Practical Exercises: Custom Schedulers and Extensibility

---

#### 1. Creating a Custom Scheduler

##### Step 1: Deploy the Default Scheduler with a Custom Name
Clone the Kubernetes Scheduler repository:
```bash
git clone https://github.com/kubernetes/kubernetes.git
cd kubernetes/staging/src/k8s.io/kube-scheduler
```

Build and modify the scheduler name:
1. Update the configuration to include a custom scheduler name, e.g., `custom-scheduler`.
2. Build the scheduler and containerize it:
   ```bash
   docker build -t custom-scheduler:latest .
   ```

Push the image to a container registry accessible by your cluster.

##### Step 2: Deploy the Custom Scheduler
Create a `custom-scheduler-deployment.yaml` file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: custom-scheduler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      component: custom-scheduler
  template:
    metadata:
      labels:
        component: custom-scheduler
    spec:
      containers:
      - name: custom-scheduler
        image: custom-scheduler:latest
        args:
        - --config=/etc/kubernetes/scheduler-config.yaml
        volumeMounts:
        - name: config-volume
          mountPath: /etc/kubernetes/
      volumes:
      - name: config-volume
        configMap:
          name: custom-scheduler-config
```

Apply the deployment:
```bash
kubectl apply -f custom-scheduler-deployment.yaml
```

---

#### 2. Assigning Pods to the Custom Scheduler

##### Step 1: Create a Pod Scheduled by the Custom Scheduler
Create a `custom-scheduler-pod.yaml` file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: custom-scheduler-pod
  annotations:
    scheduler.alpha.kubernetes.io/name: custom-scheduler
spec:
  containers:
  - name: nginx
    image: nginx
```

Apply the configuration:
```bash
kubectl apply -f custom-scheduler-pod.yaml
```

Verify the pod’s scheduler:
```bash
kubectl describe pod custom-scheduler-pod
```

Check logs for the custom scheduler to confirm scheduling decisions:
```bash
kubectl logs -l component=custom-scheduler -n kube-system
```

---

#### 3. Extending the Default Scheduler with Plugins

##### Step 1: Create a Scheduler Plugin
Implement a plugin using the Kubernetes Scheduling Framework. For example, create a scoring plugin that prioritizes nodes with the most CPU resources available:
```go
package main

import (
    "context"
    "fmt"
    "k8s.io/kubernetes/pkg/scheduler/framework"
)

type MostAvailableCPUPlugin struct{}

func (p *MostAvailableCPUPlugin) Name() string {
    return "MostAvailableCPU"
}

func (p *MostAvailableCPUPlugin) Score(ctx context.Context, state *framework.CycleState, pod *v1.Pod, nodeName string) (int64, *framework.Status) {
    nodeInfo, err := state.Read(nodeName)
    if err != nil {
        return 0, framework.NewStatus(framework.Error, fmt.Sprintf("error reading node: %v", err))
    }
    cpuAvailable := nodeInfo.Allocatable.Cpu().Value()
    return cpuAvailable, framework.NewStatus(framework.Success)
}

func (p *MostAvailableCPUPlugin) ScoreExtensions() framework.ScoreExtensions {
    return nil
}
```

Build the plugin and integrate it into the custom scheduler.

##### Step 2: Test the Plugin
Deploy the scheduler with the plugin and observe the scoring logic in action for pod placements.

---

#### Cleanup

Remove all created resources:
```bash
kubectl delete deployment custom-scheduler -n kube-system
kubectl delete pod custom-scheduler-pod
```

---
