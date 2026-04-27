# 📘 Task 2: Deploy Applications with Kubernetes Deployments

---

## 📚 Task Overview
The Nautilus DevOps team is delving into Kubernetes for app management. One team member needs to create a deployment following these details: 

- Create a deployment named `httpd` to deploy the application httpd using the image `httpd:latest` (ensure to specify the tag)

> Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Create Full Manifest File using heredoc

```yaml
cat > httpd-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpd_app
  template:
    metadata:
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
The `cat > httpd-deployment.yaml << 'EOF'` command creates a new file and writes content into it.
- `>` symbol redirects output into the file httpd-deployment.yaml.
- `<< 'EOF'` starts a heredoc block where everything written until EOF is treated as literal text.

Using quotes around `'EOF'` ensures that variables or special characters are not expanded by the shell.

### Step 2: Apply the Configuration
```bash
kubectl apply -f httpd-deployment.yaml
```
#### Explanation:
The `kubectl apply` command is used to create or update Kubernetes resources.
- `-f` flag specifies the file containing the resource definition.

If the Deployment does not exist, it will be created.

### Step 3: Verify Deployment Creation
```bash
kubectl get deployments
```
#### Explanation:
The `kubectl get` command lists Kubernetes resources.

Here, deployments specifies that we want to view all Deployments in the current namespace. It shows details such as name, ready replicas, and available replicas.

### Step 4: Verify Pods Created by Deployment
```bash
kubectl get pods
```
#### Explanation:
This command lists all Pods created in the cluster. The Deployment automatically creates and manages Pods based on the defined replica count.

### Step 5: Inspect Deployment Details
```bash
kubectl describe deployment httpd
```
#### Explanation:
The `kubectl describe` command provides detailed information about the Deployment. It shows replica status, events, Pod template details, and any errors if present.

---

## YAML/Config
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpd_app
  template:
    metadata:
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
- Incorrect selector and labels mismatch caused Pods not to be created
- Forgot to specify the correct image tag (httpd:latest)
- YAML indentation issues during initial setup
  
## 🧠 Key Learnings
- Deployments manage Pods and ensure the desired state is maintained
- Labels and selectors must match correctly for Deployment to manage Pods
- `kubectl apply` is used for declarative resource management
- Deployments provide scalability and self-healing capabilities
- `kubectl describe` helps debug issues effectively
