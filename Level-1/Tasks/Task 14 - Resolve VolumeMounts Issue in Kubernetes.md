# 📘 Task 14: Resolve VolumeMounts Issue in Kubernetes

---

## 📚 Task Overview
We encountered an issue with our Nginx and PHP-FPM setup on the Kubernetes cluster this morning, which halted its functionality. Investigate and rectify the issue:

- The pod name is `nginx-phpfpm` and configmap name is `nginx-config`. Identify and fix the problem.

- Once resolved, copy `/home/thor/index.php` file from the `jump host` to the `nginx-container` within the nginx document root. After this, you should be able to access the website using `Website` button on the top bar.

> `Note:` The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Check Pod Status

```bash
kubectl get pod nginx-phpfpm
````
#### Explanation:
The `kubectl get pod` command checks whether the Pod is running properly.

This helps confirm:
* Pod status
* Ready containers
* Restart count
  
### Step 2: Inspect Pod Details

```bash
kubectl describe pod nginx-phpfpm
```
#### Explanation:
The `kubectl describe pod` command provides detailed information about the Pod.

It shows:
* Container details
* Mounted volumes
* Events
* Restart information
* Volume mount paths

This helps identify configuration and mount-related issues.

### Step 3: Inspect ConfigMap Configuration

```bash
kubectl get configmap nginx-config -o yaml
```
#### Explanation:
The `kubectl get configmap` command retrieves the ConfigMap definition.
* `-o yaml` displays the full YAML configuration

This helps inspect the nginx configuration mounted into the nginx container.

### Step 4: Identify the Root Cause

#### Problem Found:

The nginx ConfigMap used:

```nginx
root /var/www/html;
```

However, the nginx container mounted the shared volume at:

```text
/usr/share/nginx/html
```

Because of this mismatch:
* nginx searched for files in the wrong directory
* PHP files could not be located
* Website returned errors

### Step 5: Edit ConfigMap and Fix Root Path

```bash
kubectl edit configmap nginx-config
```

Replace:

```nginx
root /var/www/html;
```

with:

```nginx
root /usr/share/nginx/html;
```

#### Explanation:
This updates the nginx document root to match the actual shared volume mount path inside the nginx container.

### Step 6: Delete Existing Pod

```bash
kubectl delete pod nginx-phpfpm
```
#### Explanation:
The Pod must be restarted to load the updated ConfigMap configuration. Since this Pod was standalone, it did not recreate automatically after deletion.

### Step 7: Recreate Pod Manifest using heredoc

```yaml
cat > nginx-phpfpm.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: nginx-phpfpm
  namespace: default
  labels:
    app: php-app

spec:
  volumes:
  - name: shared-files
    emptyDir: {}

  - name: nginx-config-volume
    configMap:
      name: nginx-config

  containers:

  - name: php-fpm-container
    image: php:7.2-fpm-alpine

    volumeMounts:
    - name: shared-files
      mountPath: /var/www/html

  - name: nginx-container
    image: nginx:latest

    volumeMounts:
    - name: nginx-config-volume
      mountPath: /etc/nginx/nginx.conf
      subPath: nginx.conf

    - name: shared-files
      mountPath: /usr/share/nginx/html
EOF
```
#### Explanation:
This manifest recreates the Pod with:
* Shared EmptyDir volume
* nginx ConfigMap mount
* php-fpm container
* nginx container

The shared volume allows both containers to access the same files.

### Step 8: Apply Pod Configuration

```bash
kubectl apply -f nginx-phpfpm.yaml
```
#### Explanation:
The `kubectl apply` command recreates the Pod using the corrected YAML manifest.

### Step 9: Verify Pod Running State

```bash
kubectl get pods
```
#### Explanation:
This command verifies that the recreated Pod is healthy and running successfully.

Expected Output:

```text
nginx-phpfpm   2/2   Running
```

### Step 10: Copy index.php File into nginx Container

```bash
kubectl cp /home/thor/index.php nginx-phpfpm:/usr/share/nginx/html/index.php -c nginx-container
```
#### Explanation:
The `kubectl cp` command copies files between the jump host and the container. This places the PHP application file inside the nginx document root.

### Step 11: Verify File Existence

```bash
kubectl exec nginx-phpfpm -c nginx-container -- ls /usr/share/nginx/html/
```
#### Explanation:
This command verifies that `index.php` exists inside the shared document root.

### Step 12: Verify Website Accessibility

```bash
kubectl exec nginx-phpfpm -c nginx-container -- curl -s localhost:8099/index.php
```
#### Explanation:
The `curl` command checks whether nginx and PHP-FPM are correctly serving the PHP application internally. The Website button should now work successfully.

---

## YAML/Config

### Corrected nginx ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config

data:
  nginx.conf: |
    events {
    }

    http {
      server {
        listen 8099 default_server;
        listen [::]:8099 default_server;

        root /usr/share/nginx/html;
        index index.html index.htm index.php;

        server_name _;

        location / {
          try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
          include fastcgi_params;
          fastcgi_param REQUEST_METHOD $request_method;
          fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
          fastcgi_pass 127.0.0.1:9000;
        }
      }
    }
```

---

## ⚠️ Challenges Faced / Troubleshooting
* nginx document root pointed to incorrect directory
* Standalone Pod did not recreate automatically after deletion
* Shared volume mount paths caused confusion initially
* Website failed because nginx could not locate PHP files
* ConfigMap changes required Pod recreation

## 🧠 Key Learnings
* ConfigMaps are commonly used for application configuration management
* nginx and PHP-FPM containers can share files using EmptyDir volumes
* Multi-container Pods share the same network namespace
* Container mount paths must align with application configuration
* Standalone Pods must be recreated manually after deletion
* `kubectl exec`, describe, and ConfigMap inspection are essential troubleshooting tools
