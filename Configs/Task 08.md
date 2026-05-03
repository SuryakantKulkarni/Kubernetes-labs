# 📘 Task 8: Schedule Cronjobs in Kubernetes

---

## YAML/Config
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: devops
spec:
  schedule: "*/12 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cron-devops
            image: httpd:latest
            command: ["/bin/sh", "-c", "echo Welcome to xfusioncorp!"]
          restartPolicy: OnFailure
```
