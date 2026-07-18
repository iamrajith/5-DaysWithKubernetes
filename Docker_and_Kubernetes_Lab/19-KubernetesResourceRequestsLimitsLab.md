# 🚀 Kubernetes Resource Requests & Limits Lab

This lab demonstrates how **ResourceQuota** and **LimitRange** (namespace‑scoped) interact with Pods, how CPU/memory stress affects scheduling, and how Kubernetes enforces stability.

---

## 🛠️ Step 1: Namespace Setup
Each participant gets a namespace based on their ID (`user01`, `user02`, etc.):

*(Replace `XX` with your participant number)*

```bash
kubectl create namespace resources-labXX
```
```bash
kubectl config set-context --current --namespace=resources-labXX
```

# Validate you are in your own namespace.
```bash
kubectl config view --minify | grep namespace:
```


👉 All quotas and limits are **namespace‑scoped**, so we’ll apply them to `resources-labXX`.

---

## 2️⃣ Apply ResourceQuota

```bash
vi quota.yaml
```

**quota.yaml**
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: resources-labXX
spec:
  hard:
    requests.cpu: "2"
    requests.memory: 2Gi
    limits.cpu: "4"
    limits.memory: 4Gi
```

```bash
kubectl apply -f quota.yaml
```

```bash
kubectl describe quota compute-quota -n resources-labXX
```

Expected output snippet:
```
Resource Quotas
 Name:       compute-quota
 Namespace:  resources-labXX
 Resource    Used  Hard
 --------    ----  ----
 requests.cpu 0     2
 limits.cpu   0     4
```

---

## 3️⃣ Apply LimitRange

```bash
vi limitrange.yaml
```

**limitrange.yaml**
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: default-limits
  namespace: resources-labXX
spec:
  limits:
  - default:
      cpu: "1"
      memory: "1Gi"
    defaultRequest:
      cpu: "500m"
      memory: "512Mi"
    type: Container
```

```bash
kubectl apply -f limitrange.yaml
```

```bash
kubectl describe limitrange default-limits -n resources-labXX
```

---

## 4️⃣ CPU Stress Deployment

```bash
vi cpu-stress.yaml
```

**cpu-stress.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cpu-stress
  namespace: resources-labXX
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cpu-stress
  template:
    metadata:
      labels:
        app: cpu-stress
    spec:
      containers:
      - name: stress
        image: polinux/stress
        args: ["--cpu", "4", "--timeout", "60s"]
        resources:
          requests:
            cpu: "500m"
            memory: "256Mi"
          limits:
            cpu: "1"
            memory: "512Mi"
```

```bash
kubectl apply -f cpu-stress.yaml
```

```bash 
kubectl get pods -n resources-labXX
```

```bash 
kubectl top pod -n resources-labXX
```

👉 Expected: Pod throttled to **1 CPU** despite trying to burn 4 cores.

---

## 5️⃣ Memory Stress Deployment

```bash
vi memory-stress.yaml
```

**memory-stress.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: memory-stress
  namespace: resources-labXX
spec:
  replicas: 1
  selector:
    matchLabels:
      app: memory-stress
  template:
    metadata:
      labels:
        app: memory-stress
    spec:
      containers:
      - name: stress
        image: polinux/stress
        args: ["--vm", "2", "--vm-bytes", "512M", "--timeout", "60s"]
        resources:
          requests:
            cpu: "100m"
            memory: "256Mi"
          limits:
            cpu: "200m"
            memory: "300Mi"
```

```bash
kubectl apply -f memory-stress.yaml
```
```bash
kubectl get pods -n resources-labXX
```

```bash
kubectl describe pod -n resources-labXX
```

👉 Expected: Pod exceeds memory limit → **OOMKilled** → restarts → eventually `CrashLoopBackOff`.

---

## 6️⃣ Watch Events

```bash
kubectl get events -n resources-labXX --sort-by=.metadata.creationTimestamp
```

Expected output snippet:
```
Warning  OOMKilled   2m   kubelet   Container stress in pod memory-stress was killed due to out of memory
Warning  BackOff     1m   kubelet   Back-off restarting failed container
Warning  FailedScheduling  30s  default-scheduler  0/3 nodes are available: Insufficient cpu.
```

---

## 7️⃣ Why Requests & Limits Matter

- **Requests** → minimum guaranteed resources for scheduling.  
- **Limits** → maximum allowed usage, preventing runaway Pods.  
- **Quota** → ensures namespaces don’t exceed agreed budgets.  
- **QoS classes** → decide eviction order under pressure:  
  - Guaranteed (last evicted)  
  - Burstable (middle)  
  - BestEffort (first evicted)  

Together, they **protect cluster stability** by:
- Preventing noisy neighbors from hogging CPU/memory.  
- Ensuring fair resource distribution across teams.  
- Allowing scheduler to make predictable placement decisions.  
- Avoiding node crashes due to uncontrolled memory usage.  

---

---

## 🔚 Cleanup

```bash
kubectl delete namespace resources-labXX
```

---
