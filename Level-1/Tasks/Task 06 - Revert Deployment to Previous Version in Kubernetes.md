# 📘 Task 6: Revert Deployment to Previous Version in Kubernetes

---

## 📚 Task Overview
Earlier today, the Nautilus DevOps team deployed a new release for an application. However, a customer has reported a bug related to this recent release. Consequently, the team aims to revert to the previous version. 

There exists a deployment named `nginx-deployment`; initiate a rollback to the previous revision. 

> Note: The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Check Deployment Rollout History

```bash
kubectl rollout history deployment/nginx-deployment
````
#### Explanation:
The `kubectl rollout history` command shows the revision history of a Deployment.

It lists all previous versions (revisions) along with change details. This helps identify available versions to which we can roll back.

### Step 2: Rollback to Previous Revision

```bash
kubectl rollout undo deployment/nginx-deployment
```
#### Explanation:
The `kubectl rollout undo` command reverts the Deployment to the previous revision.

By default, it rolls back to the immediate last version. This is useful when the latest deployment introduces issues.

### Step 3: Verify Rollback Status

```bash
kubectl rollout status deployment/nginx-deployment
```
#### Explanation:
The `kubectl rollout status` command monitors the rollback process. It ensures that the previous version is successfully restored and all Pods are updated accordingly.

### Step 4: Verify Pods After Rollback

```bash 
kubectl get pods
```
#### Explanation:
The `kubectl get pods` command lists all Pods. It helps confirm that Pods are running correctly after the rollback and are in the `Running` state.

### Step 5: Confirm Deployment Details

```bash 
kubectl describe deployment nginx-deployment
```
#### Explanation:
The `kubectl describe deployment` command provides detailed information about the Deployment. It shows the current image, revision details, and rollout events after rollback.

---

## ⚠️ Challenges Faced / Troubleshooting
* Rollback failed when no previous revision existed
* Confusion between rollout history and rollback commands
* Delay in rollback due to slow Pod termination/startup

## 🧠 Key Learnings
* Rollback helps quickly recover from faulty deployments
* `kubectl rollout undo` reverts to the previous stable version
* `kubectl rollout history` helps track deployment revisions
* Kubernetes ensures minimal downtime during rollback
* Verifying rollout status is essential after rollback
