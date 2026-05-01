# 📘 Task 7: Deploy ReplicaSet in Kubernetes Cluster

---

## 📚 Task Overview
The Nautilus DevOps team is gearing up to deploy applications on a Kubernetes cluster for migration purposes. A team member has been tasked with creating a ReplicaSet outlined below: 

- Create a ReplicaSet using `nginx` image with `latest` tag (ensure to specify as `nginx:latest`) and name it `nginx-replicaset`.

- Apply `labels`: `app` as `nginx_app`, `type` as `front-end`.

- Name the container `nginx-container`. Ensure the replica count is `4`.

> `Note:` The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Create Full Manifest File using heredoc

```yaml
cat > nginx-replicaset.yaml << 'EOF'
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx_app
    type: front-end
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx_app
  template:
    metadata:
      labels:
        app: nginx_app
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
EOF
````
#### Explanation:
The `cat > nginx-replicaset.yaml << 'EOF'` command creates a YAML file using heredoc.

* `>` redirects content into the file
* `<< 'EOF'` ensures all content is treated as literal text

The ReplicaSet configuration includes:

* `replicas: 4` to maintain four running Pods
* `selector` to identify Pods managed by the ReplicaSet
* `template` to define Pod configuration

Labels must match between selector and template for proper functioning.

### Step 2: Apply the Configuration

```bash
kubectl apply -f nginx-replicaset.yaml
```
#### Explanation:
The `kubectl apply` command creates or updates Kubernetes resources.
* `-f` flag specifies the YAML file

This will create the ReplicaSet and automatically launch 4 Pods.

### Step 3: Verify ReplicaSet Creation

```bash
kubectl get rs
```
#### Explanation:
The `kubectl get rs` command lists all ReplicaSets. It shows the desired number of replicas, current replicas, and ready replicas.

### Step 4: Verify Pods Created by ReplicaSet

```bash
kubectl get pods
```
#### Explanation:
This command lists all Pods in the current namespace. You should see 4 Pods created and managed by the ReplicaSet.

### Step 5: Inspect ReplicaSet Details

```bash
kubectl describe rs nginx-replicaset
```
#### Explanation:
The `kubectl describe rs` command provides detailed information about the ReplicaSet. It shows label selectors, Pod template details, events, and current replica status.

---

## YAML/Config

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx_app
    type: front-end
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx_app
  template:
    metadata:
      labels:
        app: nginx_app
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
```

---

## ⚠️ Challenges Faced / Troubleshooting
* Selector and template label mismatch caused Pods not to be managed by ReplicaSet
* Incorrect indentation in YAML resulted in validation errors
* Initial confusion between ReplicaSet and Deployment usage

## 🧠 Key Learnings
* ReplicaSet ensures a fixed number of Pods are always running
* Labels and selectors must match correctly for proper Pod management
* ReplicaSet automatically recreates Pods if they fail
* YAML structure is critical for correct resource creation
* ReplicaSet is a core concept behind Deployments
