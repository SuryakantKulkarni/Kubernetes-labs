# 📘 Task 11: Resolve Pod Deployment Issue

---

## 📚 Task Overview
A junior DevOps team member encountered difficulties deploying a stack on the Kubernetes cluster. The pod fails to start, presenting errors. Let's troubleshoot and rectify the issue promptly.

- There is a pod named `webserver`, and the container within it is named `httpd-container`, its utilizing the `httpd:latest` image.

- Additionally, there's a sidecar container named `sidecar-container` using the `ubuntu:latest` image.

Identify and address the issue to ensure the pod is in the `running` state and the application is accessible.

> `Note:` The `kubectl` utility on `jump_host` is configured to interact with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Check Pod Status

```bash
kubectl get pods
````
#### Explanation:
The `kubectl get pods` command lists all Pods in the current namespace.

It helps identify whether the Pod is:
* Running
* Error
* CrashLoopBackOff
* Pending

This is the first troubleshooting step.

### Step 2: Inspect Pod Details

```bash
kubectl describe pod webserver
```
#### Explanation:
The `kubectl describe pod` command provides detailed information about the Pod.

It shows:
* Container status
* Restart count
* Events
* Error messages
* Image details

This helps identify the failing container.

### Step 3: Check Sidecar Container Logs

```bash
kubectl logs webserver -c sidecar-container
```
#### Explanation:
The `kubectl logs` command retrieves logs from a container.
* `-c sidecar-container` specifies the container name inside the Pod.

This helps troubleshoot container startup failures.

### Step 4: Export Existing Pod YAML

```bash
kubectl get pod webserver -o yaml > webserver.yaml
```
#### Explanation:
The `kubectl get pod` command retrieves the Pod definition.
* `-o yaml` outputs the configuration in YAML format
* `>` redirects the output into `webserver.yaml`

This allows editing the configuration externally.

### Step 5: Edit Pod Manifest Externally using heredoc

```yaml
cat > webserver.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: webserver
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
    ports:
    - containerPort: 80

  - name: sidecar-container
    image: ubuntu:latest
    command:
    - /bin/bash
    - -c
    - while true; do sleep 3600; done
EOF
```
#### Explanation:
The `cat > webserver.yaml << 'EOF'` command creates or overwrites the YAML manifest file.
* `>` redirects content into the file
* `<< 'EOF'` starts a heredoc block

The issue was caused because the sidecar container did not have a long-running process and exited immediately. The added command keeps the sidecar container running continuously.

### Step 6: Delete Existing Faulty Pod

```bash
kubectl delete pod webserver
```
#### Explanation:
The `kubectl delete pod` command removes the existing faulty Pod from the cluster. This is required because most Pod specifications cannot be modified directly after creation.

### Step 7: Recreate Fixed Pod

```bash
kubectl apply -f webserver.yaml
```
#### Explanation:
The `kubectl apply` command creates the Pod using the corrected YAML file.
* `-f` specifies the manifest file

The new Pod will start with the corrected sidecar configuration.

### Step 8: Verify Pod Status

```bash
kubectl get pods
```
#### Explanation:
This command verifies whether the Pod is now in the `Running` state. Both containers should be healthy and operational.

### Step 9: Verify Application Accessibility

```bash
kubectl exec -it webserver -c nginx-container -- curl localhost
```
#### Explanation:
The `kubectl exec` command executes commands inside a container.
* `-it` enables interactive mode
* `-c nginx-container` specifies the nginx container
* `curl localhost` checks whether the nginx application is accessible internally

---

## YAML/Config

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webserver
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
    ports:
    - containerPort: 80

  - name: sidecar-container
    image: ubuntu:latest
    command:
    - /bin/bash
    - -c
    - while true; do sleep 3600; done
```

---

## ⚠️ Challenges Faced / Troubleshooting
* Sidecar container exited immediately after startup
* Pod remained in error state because one container was failing
* Initially checked only Pod status without inspecting logs
* Existing Pod needed to be deleted before applying corrected configuration

## 🧠 Key Learnings
* Multi-container Pods require troubleshooting each container separately
* Sidecar containers must run a valid long-running process
* `kubectl describe` and logs are essential troubleshooting tools
* Pod manifests can be edited externally using YAML files
* Faulty Pods often need to be recreated after configuration changes
