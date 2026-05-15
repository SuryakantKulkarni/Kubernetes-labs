# 📘 Task 9: Create Countdown Job in Kubernetes

---

## YAML/Config
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: countdown-nautilus
spec:
  template:
    metadata:
      name: countdown-nautilus
    spec:
      containers:
      - name: container-countdown-nautilus
        image: fedora:latest
        command: ["sleep", "5"]
      restartPolicy: Never
```
