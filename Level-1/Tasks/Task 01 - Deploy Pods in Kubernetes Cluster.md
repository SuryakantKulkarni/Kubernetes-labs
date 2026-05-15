# 📘 Task 1: Deploy Pods in Kubernetes Cluster

---

## 📚 Task Overview
The Nautilus DevOps team is diving into Kubernetes for application management. One team member has a task to create a pod according to the details below:

- Create a pod named `pod-httpd` using the `httpd` image with the `latest` tag. Ensure to specify the tag as `httpd:latest`.

- Set the `app` label to `httpd_app`, and name the container as `httpd-container`.

> Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Create Full Manifest File using heredoc

```yaml
cat > pod-httpd.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: pod-httpd
  labels:
    app: httpd_app
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
    ports:
    - containerPort: 80
EOF
```
#### Explanation:
The `cat > pod-httpd.yaml << 'EOF'` command creates a new file and writes content into it.
- `>` symbol redirects output into the file pod-httpd.yaml.
- `<< 'EOF'` starts a heredoc block where everything written until EOF is treated as literal text.

Using quotes around `'EOF'` ensures that variables or special characters are not expanded by the shell. This method is useful for creating full manifest files directly from the terminal.

### Step 2: Apply the Configuration
```bash
kubectl apply -f pod-httpd.yaml
```
#### Explanation:
The `kubectl apply` command is used to create or update Kubernetes resources.
- `-f` flag specifies the file containing the resource definition.

If the Pod does not exist, it will be created.

### Step 3: Verify Pod Creation
```bash
kubectl get pods
```
#### Explanation:
The `kubectl get` command lists Kubernetes resources. 

Here, pods specifies that we want to view all Pods in the current namespace. It shows details such as name, status, readiness, and age.

### Step 4: Inspect Pod Details
```bash
kubectl describe pod pod-httpd
```
#### Explanation:
The `kubectl describe` command provides detailed information about a resource. It shows container details, events, node assignment, and possible errors.

### Step 5: Check Pod Logs
```bash
kubectl logs pod-httpd
```
#### Explanation:
The `kubectl logs` command retrieves logs from the container inside the Pod. Since this Pod has only one container, logs are displayed directly.

---

## YAML/Config
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-httpd
  labels:
    app: httpd_app
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
    ports:
    - containerPort: 80
```

---

## ⚠️ Challenges Faced / Troubleshooting
- YAML indentation issues caused deployment errors
- Missed specifying the correct image tag initially
- Typo in Pod name while executing commands

## 🧠 Key Learnings
- Heredoc with 'EOF' prevents variable expansion and ensures accurate file creation
- Pods can be created using YAML manifests
- Labels help organize and manage Kubernetes resources
- kubectl describe and logs are essential for debugging
- Terminal-based manifest creation is efficient in real-world DevOps workflows
