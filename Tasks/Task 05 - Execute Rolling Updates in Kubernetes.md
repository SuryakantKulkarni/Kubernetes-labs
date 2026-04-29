# 📘 Task 5: Execute Rolling Updates in Kubernetes

---

## 📚 Task Overview
An application currently running on the Kubernetes cluster employs the nginx web server. The Nautilus application development team has introduced some recent changes that need deployment. They've crafted an image `nginx:1.19` with the latest updates. 

Execute a rolling update for this application, integrating the `nginx:1.19` image. The deployment is named `nginx-deployment`. 

Ensure all pods are operational post-update. 

> Note: The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Update Deployment Image

```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.19
```
#### Explanation:
The `kubectl set image` command is used to update the container image of a resource.
- `deployment/nginx-deployment` specifies the Deployment to update
- nginx=nginx:1.19 means:
  - nginx-container → container name inside the Deployment
  - nginx:1.19 → new image with updated version

This triggers a rolling update of the Deployment.

### Step 2: Verify Rolling Update Status
```bash
kubectl rollout status deployment/nginx-deployment
```
#### Explanation:
The `kubectl rollout status` command monitors the progress of a rollout. It ensures that all new Pods are successfully created and old ones are terminated without errors.

### Step 3: Verify Pods After Update
```bash
kubectl get pods
```
#### Explanation:
The `kubectl get pods` command lists all Pods in the current namespace. It helps verify that new Pods are running with the updated image and are in the Running state.

### Step 4: Confirm Updated Image
```bash
kubectl describe deployment nginx-deployment
```
#### Explanation:
The `kubectl describe deployment` command provides detailed information about the Deployment. It shows the updated container image `(nginx:1.19)` along with rollout events and replica status.

---

## ⚠️ Challenges Faced / Troubleshooting
- Incorrect container name caused image update to fail
- Deployment name typo resulted in command execution error
- Delay in rollout due to slow image pull

## 🧠 Key Learnings
- Rolling updates allow zero-downtime application upgrades
- `kubectl set image` is used to update container images in Deployments
- `kubectl rollout status` helps track update progress
- Kubernetes ensures availability during updates by gradually replacing Pods
- Verifying rollout is essential to ensure application stability
