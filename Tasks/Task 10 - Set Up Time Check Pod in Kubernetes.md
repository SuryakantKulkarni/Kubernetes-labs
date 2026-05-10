# 📘 Task 10: Set Up Time Check Pod in Kubernetes

---

## 📚 Task Overview
The Nautilus DevOps team needs a time check pod created in a specific Kubernetes namespace for logging purposes. Initially, it's for testing, but it may be integrated into an existing cluster later. Here's what's required: 

-  Create a pod called `time-check` in the `xfusion` namespace. The pod should contain a container named `time-check`, utilizing the `busybox` image with the `latest` tag (specify as `busybox:latest`).

-  Create a config map named `time-config` with the data `TIME_FREQ=2` in the same namespace.

-  Configure the `time-check` container to execute the command: `while true; do date; sleep $TIME_FREQ;done`. Ensure the result is written `/opt/finance/time/time-check.log`. Also, add an environmental variable `TIME_FREQ` in the container, fetching its value from the config map TIME_FREQ` key.

-  Create a volume `log-volume` and mount it at `/opt/finance/time` within the container.

> Note: The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## ⚙️ Step-by-Step Implementation

### Step 1: Create Namespace

```bash
kubectl create namespace xfusion
````
#### Explanation:
The `kubectl create namespace` command is used to create a new namespace.
- `xfusion` is the namespace where all resources will be created.

### Step 2: Create ConfigMap using heredoc

```yaml
cat > time-config.yaml << 'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: time-config
  namespace: xfusion
data:
  TIME_FREQ: "2"
EOF
```
#### Explanation:
This command creates a ConfigMap YAML file.
* `TIME_FREQ: "2"` defines a key-value pair

This value will be used inside the container as an environment variable

### Step 3: Apply ConfigMap

```bash
kubectl apply -f time-config.yaml
```
#### Explanation:
The `kubectl apply` command creates the ConfigMap in the cluster.
* `-f` specifies the file

### Step 4: Create Pod Manifest using heredoc

```yaml
cat > time-check-pod.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: time-check
  namespace: xfusion
spec:
  containers:
  - name: time-check
    image: busybox:latest
    command: ["/bin/sh", "-c", "while true; do date; sleep $TIME_FREQ; done >> /opt/finance/time/time-check.log"]
    env:
    - name: TIME_FREQ
      valueFrom:
        configMapKeyRef:
          name: time-config
          key: TIME_FREQ
    volumeMounts:
    - name: log-volume
      mountPath: /opt/finance/time
  volumes:
  - name: log-volume
    emptyDir: {}
EOF
```
#### Explanation:
This YAML defines the Pod with required configurations:
* `command` runs a loop to print date and sleep based on TIME_FREQ
* Output is redirected to `/opt/finance/time/time-check.log`
* `env` fetches value from ConfigMap
* `volumeMounts` mounts storage inside container
* `emptyDir` volume provides temporary storage

### Step 5: Apply Pod Configuration

```bash
kubectl apply -f time-check-pod.yaml
```
#### Explanation:
This command creates the Pod inside the `xfusion` namespace.

### Step 6: Verify Pod in Namespace

```bash
kubectl get pods -n xfusion
```
#### Explanation:
The `-n xfusion` flag ensures we check resources inside the correct namespace.

### Step 7: Check Logs File inside Pod

```bash
kubectl exec -it time-check -n xfusion -- cat /opt/finance/time/time-check.log
```
#### Explanation:
The `kubectl exec` command runs commands inside the container.
* `-it` enables interactive mode
* `--` separates kubectl arguments from the command
* `cat` reads the log file generated inside the Pod

---

## YAML/Config

### ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: time-config
  namespace: xfusion
data:
  TIME_FREQ: "2"
```

### Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: time-check
  namespace: xfusion
spec:
  containers:
  - name: time-check
    image: busybox:latest
    command: ["/bin/sh", "-c", "while true; do date; sleep $TIME_FREQ; done >> /opt/finance/time/time-check.log"]
    env:
    - name: TIME_FREQ
      valueFrom:
        configMapKeyRef:
          name: time-config
          key: TIME_FREQ
    volumeMounts:
    - name: log-volume
      mountPath: /opt/finance/time
  volumes:
  - name: log-volume
    emptyDir: {}
```

---

## ⚠️ Challenges Faced / Troubleshooting
* Forgot to create namespace before applying resources
* Incorrect ConfigMap reference caused environment variable not to load
* Command syntax issues inside YAML (especially shell loop)
* Log file path not created due to missing volume mount

## 🧠 Key Learnings
* ConfigMaps are used to pass configuration data into containers
* Environment variables can be injected from ConfigMaps
* Volumes allow data persistence inside containers
* `emptyDir` provides temporary storage for Pods
* `kubectl exec` helps debug and verify container behavior
* Combining ConfigMap + Volume + Command is a real-world use case
