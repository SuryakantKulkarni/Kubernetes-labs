# 📘 Task 10: Set Up Time Check Pod in Kubernetes

---

## 📚 Task Overview
A junior DevOps team member encountered difficulties deploying a stack on the Kubernetes cluster. The pod fails to start, presenting errors. Let's troubleshoot and rectify the issue promptly. 

- There is a `pod` named `webserver`, and the `container` within it is named `httpd-container`, its utilizing the `httpd:latest` image. 

- Additionally, there's a sidecar container named `sidecar-container` using the `ubuntu:latest` image. 

- Identify and address the issue to ensure the pod is in the running state and the application is accessible. 

> Note: The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Check Pod Status

```bash
kubectl get pods
````
#### Explanation:
The `kubectl get pods` command lists all Pods in the current namespace.

It helps identify the current status of the Pod such as:
* Running
* CrashLoopBackOff
* Error
* Pending

This is the first step in troubleshooting Kubernetes resources.

### Step 2: Inspect Pod Details

```bash
kubectl describe pod webserver
```
#### Explanation:
The `kubectl describe pod` command provides detailed information about the Pod.

It displays:
* Container status
* Events
* Restart counts
* Image details
* Error messages

This command helps identify why the Pod is failing.

### Step 3: Check Container Logs

```bash
kubectl logs webserver -c sidecar-container
```
#### Explanation:
The `kubectl logs` command retrieves logs from a container.
* `-c sidecar-container` specifies the container name inside the Pod.

This helps diagnose issues related to container startup or command execution.

### Step 4: Edit the Pod Configuration

```bash
kubectl edit pod webserver
```
#### Explanation:
The `kubectl edit` command opens the live resource configuration in a text editor. It allows direct modification of the Pod definition.

During troubleshooting, it was identified that the sidecar container was exiting immediately because no valid long-running process was defined.

To fix the issue, update the sidecar container configuration as shown below:

```yaml
- name: sidecar-container
  image: ubuntu:latest
  command: ["/bin/bash", "-c", "while true; do sleep 3600; done"]
```

This command keeps the sidecar container running continuously.

### Step 5: Verify Pod Status After Fix

```bash
kubectl get pods
```
#### Explanation:
Re-run the `kubectl get pods` command to confirm the Pod is now in the `Running` state.

A healthy Pod should display:
* STATUS as `Running`
* READY containers count correctly

### Step 6: Verify Application Accessibility

```bash
kubectl exec -it webserver -c httpd-container -- curl localhost
```
#### Explanation:
The `kubectl exec` command runs commands inside a container.
* `-it` enables interactive terminal mode
* `-c httpd-container` specifies the target container
* `curl localhost` checks whether the HTTPD application is responding internally

This confirms the application is accessible after troubleshooting.

---

## YAML/Config

### Updated Sidecar Container Configuration

```yaml
- name: sidecar-container
  image: ubuntu:latest
  command: ["/bin/bash", "-c", "while true; do sleep 3600; done"]
```

---

## ⚠️ Challenges Faced / Troubleshooting

* Sidecar container exited immediately after startup
* Pod entered error state because one container was continuously failing
* Initially checked only Pod status without inspecting logs
* Multi-container Pods require troubleshooting each container individually

## 🧠 Key Learnings

* A Pod remains unhealthy if any critical container fails
* `kubectl describe` and `kubectl logs` are essential troubleshooting tools
* Sidecar containers must run a valid long-running process
* `kubectl edit` allows quick live configuration fixes
* Multi-container Pods require container-specific debugging
