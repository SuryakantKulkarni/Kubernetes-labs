# 📘 Task 13: Expose Application Using NodePort Service in Kubernetes

---

## 📚 Task Overview
The Nautilus DevOps team has already deployed a `ReplicaSet` to host an application that requires a highly available infrastructure. Your task is to expose the application running in the existing `ReplicaSet` by creating a Kubernetes `NodePort` Service.

Follow the specifications below to create the Service and ensure the application pods are accessible:

-  A ReplicaSet named `nginx-replicaset` is already running in the cluster.

- The pods managed by the ReplicaSet use the following labels:

  Assign labels `app` as `nginx_app`, and `type` as `front-end`.

- Create a NodePort Service named `nginx-service` to expose the application.

- Set the NodePort to `30080`.

- Expose port `80` of the application.

> `Note:` Do not delete or modify the configuration of the deployed `ReplicaSet` application.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Verify Existing ReplicaSet

```bash
kubectl get rs
````
#### Explanation:
The `kubectl get rs` command lists all ReplicaSets in the current namespace. This confirms that the `nginx-replicaset` already exists and is running.

### Step 2: Verify Pod Labels

```bash
kubectl get pods --show-labels
```
#### Explanation:
The `kubectl get pods --show-labels` command displays Pods along with their labels. This helps verify the labels used by the ReplicaSet Pods.

The Service selector must match these labels to route traffic correctly.

### Step 3: Create Service Manifest using heredoc

```yaml
cat > nginx-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx_app
    type: front-end
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
EOF
```
#### Explanation:
The `cat > nginx-service.yaml << 'EOF'` command creates a YAML manifest file using heredoc.

Service configuration details:
* `type: NodePort` exposes the application externally
* `selector` matches the labels of ReplicaSet Pods
* `port: 80` exposes the Service internally
* `targetPort: 80` forwards traffic to container port 80
* `nodePort: 30080` exposes the application externally on cluster nodes

### Step 4: Apply Service Configuration

```bash
kubectl apply -f nginx-service.yaml
```
#### Explanation:
The `kubectl apply` command creates the Service resource.
* `-f` specifies the YAML file containing the Service definition

The Service starts routing traffic to matching Pods immediately.

### Step 5: Verify Service Creation

```bash
kubectl get services
```
#### Explanation:
The `kubectl get services` command lists all Services.

It verifies:
* Service name
* Service type
* Cluster IP
* NodePort mapping

You should see:

```text
nginx-service   NodePort   <cluster-ip>   <none>   80:30080/TCP
```

### Step 6: Verify Service Details

```bash
kubectl describe service nginx-service
```
#### Explanation:
The `kubectl describe service` command provides detailed information about the Service.

It shows:
* Label selectors
* Endpoints
* Port mappings
* Connected Pods

This helps confirm the Service is correctly linked to ReplicaSet Pods.

---

## YAML/Config

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx_app
    type: front-end
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

---

## ⚠️ Challenges Faced / Troubleshooting
* Incorrect label selectors prevented Service from routing traffic
* NodePort value outside valid range caused validation errors
* Initially confused between `port`, `targetPort`, and `nodePort`
* Service endpoints were empty due to label mismatch

## 🧠 Key Learnings
* Services expose Kubernetes applications for internal or external access
* NodePort Services allow external access using node IP and port
* Label selectors connect Services to Pods
* `targetPort` forwards traffic to the container port
* Correct label matching is essential for Service communication
