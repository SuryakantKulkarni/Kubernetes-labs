# 📘 Task 9: Create Countdown Job in Kubernetes

---

## 📚 Task Overview
The Nautilus DevOps team is crafting jobs in the Kubernetes cluster. While they're developing actual scripts/commands, they're currently setting up templates and testing jobs with dummy commands. Please create a job template as per details given below:

- Create a job named `countdown-nautilus`.

- The spec template should be named `countdown-nautilus` (under metadata), and the container should be named `container-countdown-nautilus`.

- Utilize image `fedora` with `latest` tag (ensure to specify as `fedora:latest`), and set the restart policy to `Never`.

- Execute the command `sleep 5`.

> Note: The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Create Full Manifest File using heredoc

```bash
cat > countdown-nautilus-job.yaml << 'EOF'
apiVersion: batch/v1
kind: Job
metadata:
  name: countdown-nautilus
spec:
  template:
    metadata:
      name: countdown-nautilus
    spec:
      containers:
      - name: container-countdown-nautilus
        image: fedora:latest
        command: ["sleep", "5"]
      restartPolicy: Never
EOF
````
#### Explanation:
The `cat > countdown-nautilus-job.yaml << 'EOF'` command creates a YAML file using heredoc.
* `>` redirects content into the file
* `<< 'EOF'` ensures all content is treated as literal text

The Job configuration includes:
* `template` which defines the Pod to run
* `containers` section specifying the container details
* `command` to execute (`sleep 5`)
* `restartPolicy: Never` ensures the container does not restart after completion

### Step 2: Apply the Configuration

```bash
kubectl apply -f countdown-nautilus-job.yaml
```
#### Explanation:
The `kubectl apply` command creates or updates Kubernetes resources.
* `-f` flag specifies the YAML file

This will create the Job in the cluster.

### Step 3: Verify Job Creation

```bash
kubectl get jobs
```
#### Explanation:
The `kubectl get jobs` command lists all Jobs in the current namespace. It shows the completion status, duration, and number of successful runs.

### Step 4: Check Pods Created by Job

```bash
kubectl get pods
```
#### Explanation:
This command lists all Pods. 

Each Job creates a Pod to execute the task. The Pod will complete once the command finishes execution.

### Step 5: Inspect Job Details

```bash
kubectl describe job countdown-nautilus
```
#### Explanation:
The `kubectl describe job` command provides detailed information about the Job. It shows Pod status, events, and execution details.

---

## YAML/Config

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: countdown-nautilus
spec:
  template:
    metadata:
      name: countdown-nautilus
    spec:
      containers:
      - name: container-countdown-nautilus
        image: fedora:latest
        command: ["sleep", "5"]
      restartPolicy: Never
```

---

## ⚠️ Challenges Faced / Troubleshooting
* Incorrect restart policy caused validation errors
* YAML indentation issues in template section
* Confusion between Job and CronJob usage

## 🧠 Key Learnings
* Jobs are used for one-time task execution in Kubernetes
* Each Job creates a Pod to complete the assigned task
* `restartPolicy: Never` ensures the Pod runs only once
* Commands can be executed directly inside containers
* Jobs are useful for batch processing and automation tasks
