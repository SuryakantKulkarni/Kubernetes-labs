# 📘 Task 1: Deploy Pods in Kubernetes Cluster

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
