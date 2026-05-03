# 📘 Task 8: Schedule Cronjobs in Kubernetes

---

## 📚 Task Overview
The Nautilus DevOps team is setting up recurring tasks on different schedules. Currently, they're developing scripts to be executed periodically. To kickstart the process, they're creating cron jobs in the Kubernetes cluster with placeholder commands. Follow the instructions below: 

- Create a cronjob named `devops`.

- Set Its schedule to something like `*/12 * * * *`. You can set any schedule for now.

- Name the container `cron-devops`.

- Utilize the `httpd` image with `latest tag` (specify as `httpd:latest`).

- Execute the dummy command `echo Welcome to xfusioncorp!`.

- Ensure the restart policy is `OnFailure`.

> `Note:` The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Create Full Manifest File using heredoc

```bash
cat > cronjob-devops.yaml << 'EOF'
apiVersion: batch/v1
kind: CronJob
metadata:
  name: devops
spec:
  schedule: "*/12 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cron-devops
            image: httpd:latest
            command: ["/bin/sh", "-c", "echo Welcome to xfusioncorp!"]
          restartPolicy: OnFailure
EOF
````
#### Explanation:
The `cat > cronjob-devops.yaml << 'EOF'` command creates a YAML file using heredoc.
* `>` redirects content into the file
* `<< 'EOF'` ensures all content is treated as literal text

The CronJob configuration includes:
* `schedule` defines when the job runs (every 12 minutes here)
* `jobTemplate` defines the Job to execute
* `command` specifies the task to run inside the container
* `restartPolicy: OnFailure` ensures the container restarts only if it fails

### Step 2: Apply the Configuration

```bash
kubectl apply -f cronjob-devops.yaml
```
#### Explanation:
The `kubectl apply` command is used to create or update Kubernetes resources.
* `-f` flag specifies the YAML file

This will create the CronJob in the cluster.

### Step 3: Verify CronJob Creation

```bash
kubectl get cronjobs
```
#### Explanation:
The `kubectl get cronjobs` command lists all CronJobs. It shows details such as schedule, last run time, and active jobs.

### Step 4: Check Jobs Created by CronJob

```bash
kubectl get jobs
```
#### Explanation:
The `kubectl get jobs` command lists Jobs created by the CronJob. Each execution of the CronJob creates a new Job.

### Step 5: Inspect CronJob Details

```bash
kubectl describe cronjob devops
```
#### Explanation:
The `kubectl describe cronjob` command provides detailed information about the CronJob. It shows schedule, job history, events, and execution details.

---

## YAML/Config

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: devops
spec:
  schedule: "*/12 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cron-devops
            image: httpd:latest
            command: ["/bin/sh", "-c", "echo Welcome to xfusioncorp!"]
          restartPolicy: OnFailure
```

---

## ⚠️ Challenges Faced / Troubleshooting
* Incorrect cron schedule format caused validation errors
* Missed `restartPolicy`, which is mandatory in CronJob
* YAML indentation issues in jobTemplate section

## 🧠 Key Learnings
* CronJobs are used to run scheduled tasks in Kubernetes
* Cron schedule format follows standard Linux cron syntax
* Each CronJob execution creates a new Job
* `restartPolicy: OnFailure` ensures reliability of task execution
* Proper YAML structure is critical for CronJob configuration
