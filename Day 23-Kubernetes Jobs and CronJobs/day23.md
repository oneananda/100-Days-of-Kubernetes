---

## Day 23: Kubernetes Jobs and CronJobs — Running Tasks in Kubernetes

### 📘 Overview

**Kubernetes Jobs and CronJobs** are used to run short-lived or periodic tasks in a cluster. Jobs ensure that a specified number of tasks are completed successfully, while CronJobs schedule tasks to run at specific intervals. Today’s session focuses on understanding, creating, and managing Jobs and CronJobs.

---

### What are Jobs and CronJobs?

1. **Job**:
   - Manages the execution of a task until it completes successfully.
   - Used for one-off tasks, such as data processing or backups.

2. **CronJob**:
   - Manages Jobs that run on a recurring schedule.
   - Ideal for periodic tasks like log rotation or database cleanups.

---

### Key Concepts

1. **Completion Criteria**:
   - A Job is considered complete when the specified number of successful pods (`completions`) is reached.

2. **Parallelism**:
   - Jobs can run multiple pods in parallel using the `parallelism` field.

3. **Scheduling with CronJobs**:
   - CronJobs use a standard cron expression to schedule tasks.

---


### Hands-On with Jobs and CronJobs

In today’s session, we’ll create and manage Jobs and CronJobs, exploring their features and practical use cases.

---

### 1. Creating a Simple Job

Deploy a Job to print a message. Save the following YAML as `simple-job.yaml`:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: simple-job
spec:
  template:
    spec:
      containers:
      - name: simple-job
        image: busybox
        command: ["echo", "Hello from Kubernetes Job!"]
      restartPolicy: Never
  backoffLimit: 4
```

#### Apply the Job:
```bash
kubectl apply -f simple-job.yaml
```

#### Verify the Job:
```bash
kubectl get jobs
kubectl logs <pod-name>
```

---

