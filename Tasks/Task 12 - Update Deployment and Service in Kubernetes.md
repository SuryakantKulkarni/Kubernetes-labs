# 📘 Task 12: Update Deployment and Service in Kubernetes

---

## 📚 Task Overview
An application deployed on the Kubernetes cluster requires an update with new features developed by the Nautilus application development team. The existing setup includes a deployment named `nginx-deployment` and a service named `nginx-service`. Below are the necessary changes to be implemented without deleting the deployment and service: 

- Modify the service nodeport from `30008` to `32165`
  
- Change the replicas count from `1` to `5`

- Update the image from `nginx:1.17` to `nginx:latest`

> `Note:` The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Verify Existing Deployment and Service

```bash
kubectl get deployments,services
````
#### Explanation:
The `kubectl get` command lists Kubernetes resources.
* `deployments` displays all Deployments
* `services` displays all Services

This helps confirm that the required resources already exist before making updates.

### Step 2: Update Deployment Replicas

```bash
kubectl scale deployment nginx-deployment --replicas=5
```
#### Explanation:
The `kubectl scale` command changes the number of Pod replicas managed by a Deployment.
* `deployment nginx-deployment` specifies the target Deployment
* `--replicas=5` sets the desired number of replicas to 5

Kubernetes automatically creates additional Pods to match the desired count.

### Step 3: Check Actual Container Name

```bash
kubectl describe deployment nginx-deployment
```
#### Explanation:
The `kubectl describe deployment` command shows detailed information about the Deployment.

Under the `Containers:` section, it displays the actual container name used inside the Deployment.

This helps avoid errors while updating container images.

Example:

```text
Containers:
  nginx-container:
```

### Step 4: Update Deployment Image

```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:latest
```
#### Explanation:
The `kubectl set image` command updates the container image of a Deployment.
* `nginx-container` specifies the container name
* `nginx:latest` specifies the updated image

This triggers a rolling update without downtime.

### Step 5: Update Service NodePort

```bash
kubectl edit service nginx-service
```
#### Explanation:
The `kubectl edit service` command opens the Service configuration in a text editor.

Locate the `nodePort` field under the `ports` section and update its value:

```yaml
nodePort: 32165
```

Save and exit the editor to apply changes.

### Step 6: Verify Deployment Rollout Status

```bash
kubectl rollout status deployment/nginx-deployment
```
#### Explanation:
The `kubectl rollout status` command monitors the Deployment rollout process. It ensures all updated Pods are created successfully and become available.

### Step 7: Verify Updated Resources

```bash
kubectl get deployments,services,pods
```
#### Explanation:
This command verifies:
* Deployment replica count
* Service NodePort
* Pod status

It confirms that all updates were applied successfully.

---

## YAML/Config

### Deployment Configuration

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment

spec:
  replicas: 5

  template:
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
```

### Service Configuration

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service

spec:
  type: NodePort

  ports:
  - port: 80
    targetPort: 80
    nodePort: 32165
```

---

## ⚠️ Challenges Faced / Troubleshooting
* Incorrect container name caused image update failure
* Initially assumed Deployment name and container name were the same
* Delay in rollout while new Pods were being created
* Editing Service YAML incorrectly caused validation issues

## 🧠 Key Learnings
* Deployments can be updated without deleting existing resources
* `kubectl scale` dynamically adjusts replica count
* `kubectl set image` performs rolling updates on Deployments
* Container name must be verified before updating images
* Services can be modified using `kubectl edit`
* Kubernetes ensures high availability during updates
