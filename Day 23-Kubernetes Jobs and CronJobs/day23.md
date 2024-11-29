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


### 2. Running a Parallel Job

Deploy a Job with multiple parallel tasks. Save the following YAML as `parallel-job.yaml`:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: parallel-job
spec:
  parallelism: 3
  completions: 6
  template:
    spec:
      containers:
      - name: parallel-job
        image: busybox
        command: ["echo", "Task completed!"]
      restartPolicy: Never
```

#### Apply the Job:
```bash
kubectl apply -f parallel-job.yaml
```

#### Verify the Parallelism:
```bash
kubectl get jobs
kubectl get pods -o wide
```

---


### 3. Creating a CronJob

Deploy a CronJob to print a message every minute. Save the following YAML as `simple-cronjob.yaml`:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: simple-cronjob
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: simple-cronjob
            image: busybox
            command: ["echo", "Hello from Kubernetes CronJob!"]
          restartPolicy: Never
```

#### Apply the CronJob:
```bash
kubectl apply -f simple-cronjob.yaml
```

#### Verify the CronJob:
```bash
kubectl get cronjobs
kubectl get jobs
kubectl logs <pod-name>
```

---


### 4. Managing Failed Jobs and CronJobs

#### Set `backoffLimit` for Retries:
Add or modify the `backoffLimit` field to specify the number of retries for a failed Job.

#### Clean Up Old Jobs:
Configure the `successfulJobsHistoryLimit` and `failedJobsHistoryLimit` fields in the CronJob to limit the number of retained Job histories.

```yaml
spec:
  successfulJobsHistoryLimit: 3
  failedJobsHistoryLimit: 1
```

---


### 5. Practical Use Case: Database Backup with CronJob

Deploy a CronJob to back up a database daily:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: db-backup
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: db-backup
            image: alpine
            command: ["/bin/sh", "-c", "echo 'Backing up database...' && sleep 10"]
          restartPolicy: Never
```

#### Apply the CronJob:
```bash
kubectl apply -f db-backup.yaml
```

---


### 📝 Document Your Progress

In your `day23.md` file, record:
- YAML configurations for Jobs and CronJobs.
- Steps and observations on parallelism, scheduling, and retry mechanisms.
- Insights on managing task automation in Kubernetes.

---

### 🎯 Outcome for Day 23

By the end of Day 23, you should:
1. Understand the purpose of Jobs and CronJobs in Kubernetes.
2. Be able to create and manage one-off and recurring tasks.
3. Know how to handle parallelism and retries in Jobs.
4. Automate periodic tasks using CronJobs effectively.

---

### 🔗 Additional Resources

- [Kubernetes Documentation: Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/job/)
- [Kubernetes Documentation: CronJobs](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)

---