# 📘 Task 4: Set Resource Limits in Kubernetes Pods

---

## 📚 Task Overview
The Nautilus DevOps team has noticed performance issues in some Kubernetes-hosted applications due to resource constraints. To address this, they plan to set limits on resource utilization. Here are the details:

Create a pod named `httpd-pod` with a container named `httpd-container`. Use the `httpd` image with the `latest` tag (specify as `httpd:latest`). Set the following resource limits:

- Requests: Memory: `15Mi`, CPU: `100m`

- Limits: Memory: `20Mi`, CPU: `100m`

> Note: The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Create Full Manifest File using heredoc

```yaml
cat > httpd-pod.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
    resources:
      requests:
        memory: "15Mi"
        cpu: "100m"
      limits:
        memory: "20Mi"
        cpu: "100m"
    ports:
    - containerPort: 80
EOF
```

#### Explanation:
The `cat > httpd-pod.yaml << 'EOF'` command creates a YAML file using heredoc.
- `>` redirects content into the file
- `<< 'EOF'` ensures all content is treated as literal text

The `resources` section defines resource allocation:
- `requests` specify the minimum guaranteed resources
- `limits` specify the maximum resources the container can use

### Step 2: Apply the Configuration
```bash
kubectl apply -f httpd-pod.yaml
```
#### Explanation:
The `kubectl apply` command is used to create or update Kubernetes resources.
- `-f` flag specifies the YAML file

If the Pod does not exist, it will be created with defined resource constraints.

### Step 3: Verify Pod Creation
```bash
kubectl get pods
```
#### Explanation:
The `kubectl get pods` command lists all Pods in the current namespace. It shows the Pod name, status, readiness, and age.

### Step 4: Inspect Pod Resource Configuration
```bash
kubectl describe pod httpd-pod
```
#### Explanation:
The `kubectl describe pod` command provides detailed information about the Pod. It displays resource requests and limits under the container section along with events and status.

### Step 5: Verify Resource Allocation (Optional Advanced Check)
```bash
kubectl top pod httpd-pod
```
#### Explanation:
The `kubectl top pod` command shows real-time CPU and memory usage of the Pod. 

This helps verify whether the container is running within the defined resource limits.
(Note: Requires Metrics Server to be installed in the cluster.)

---

## YAML/Config
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
    resources:
      requests:
        memory: "15Mi"
        cpu: "100m"
      limits:
        memory: "20Mi"
        cpu: "100m"
    ports:
    - containerPort: 80
```

---

## ⚠️ Challenges Faced / Troubleshooting
- Incorrect resource unit formatting caused validation errors
- Confusion between `requests` and `limits` initially
- YAML indentation issues while defining resource blocks
  
## 🧠 Key Learnings
- Resource requests define minimum guaranteed resources for scheduling
- Resource limits restrict maximum resource usage of a container
- Proper resource management improves cluster performance and stability
- `kubectl describe` helps verify resource configuration
- Monitoring tools like `kubectl top` help track actual usage
