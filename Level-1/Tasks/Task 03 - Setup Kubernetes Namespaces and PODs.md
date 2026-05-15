# 📘 Task 3: Setup Kubernetes Namespaces and PODs

---

## 📚 Task Overview
The Nautilus DevOps team is planning to deploy some micro services on Kubernetes platform. The team has already set up a Kubernetes cluster and now they want to set up some namespaces, deployments etc. Based on the current requirements, the team has shared some details as below: 

Create a namespace named `dev` and deploy a POD within it. Name the pod `dev-nginx-pod` and use the `nginx` image with the `latest` tag. Ensure to specify the tag as `nginx:latest`. 

> Note: The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Create Namespace

```bash
kubectl create namespace dev
```
#### Explanation:
The `kubectl create namespace` command is used to create a new namespace in Kubernetes.

Here, dev is the name of the namespace. Namespaces help organize and isolate resources within a cluster.

### Step 2: Create Full Manifest File using heredoc
```yaml
cat > dev-nginx-pod.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: dev-nginx-pod
  namespace: dev
  labels:
    app: nginx_app
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
    ports:
    - containerPort: 80
EOF
```
#### Explanation:
The `cat > dev-nginx-pod.yaml << 'EOF'` command creates a new YAML file.
- `>` redirects the content into the file
- `<< 'EOF'` starts a heredoc block and treats everything as literal text

The `namespace: dev` field ensures that the Pod is created inside the dev namespace.

### Step 3: Apply the Configuration
```bash
kubectl apply -f dev-nginx-pod.yaml
```
#### Explanation:
The `kubectl apply` command creates or updates resources.
- `-f` specifies the file containing the resource definition

Since the namespace is already defined in the YAML, the Pod will be created inside the dev namespace.

### Step 4: Verify Pod in Namespace
```bash
kubectl get pods -n dev
```
#### Explanation:
The `kubectl get pods` command lists Pods.
- `-n dev` specifies the namespace

This ensures we are viewing Pods inside the dev namespace instead of the default namespace.

### Step 5: Inspect Pod Details
```bash
kubectl describe pod dev-nginx-pod -n dev
```
#### Explanation:
The `kubectl describe` command shows detailed information about a resource.
- `dev-nginx-pod` is the Pod name
- `-n dev` ensures the command targets the correct namespace

It displays events, container status, and other debugging information.

---

## YAML/Config
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dev-nginx-pod
  namespace: dev
  labels:
    app: nginx_app
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
    ports:
    - containerPort: 80
```

---

## ⚠️ Challenges Faced / Troubleshooting
- Forgot to specify the namespace in YAML, causing Pod to be created in default namespace
- Used kubectl get pods without -n dev, which showed no resources
- Minor YAML indentation errors during initial creation
  
## 🧠 Key Learnings
- Namespaces help isolate and organize Kubernetes resources
- Resources can be assigned to a namespace using the namespace field in YAML
- `-n` flag is essential when working with specific namespaces
- Pods can be deployed inside namespaces for better resource management
- Proper verification commands are important to avoid confusion
