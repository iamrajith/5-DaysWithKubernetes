# ⚡ Kubernetes HPA Demo Lab

## 1️⃣ Namespace Setup
---

Make use of the same namespace which used in the previous LAB.

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

---

## 2️⃣ Recommended Image for HPA
Use the official **`k8s.gcr.io/hpa-example`** image (also mirrored as `registry.k8s.io/hpa-example`).  
It runs a simple HTTP server that burns CPU when you hit `/` with requests — perfect for autoscaling demos.

---

## 3️⃣ Deployment Definition

```bash
vi hpa-deploy.yaml
```

**hpa-deploy.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hpa-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hpa-demo
  template:
    metadata:
      labels:
        app: hpa-demo
    spec:
      containers:
      - name: hpa-demo
        image: registry.k8s.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 200m
          limits:
            cpu: 500m
```

```bash
kubectl apply -f hpa-deploy.yaml
```

---

## 4️⃣ Expose Service

```bash
kubectl expose deployment hpa-demo \
  --type=NodePort \
  --port=80 
```

---

## 5️⃣ Create HPA

```bash
vi hpa.yaml
```

**hpa.yaml**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: hpa-demo
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: hpa-demo
  minReplicas: 1
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

```bash
kubectl apply -f hpa.yaml
kubectl get hpa 
```

Expected output:
```
NAME       REFERENCE             TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
hpa-demo   Deployment/hpa-demo   10%/50%   1         5         1          1m
```

---

## 6️⃣ Generate Load

Use `kubectl run` with busybox to send requests:

```bash
kubectl run -i --tty load-generator --rm \
  --image=busybox \
  --restart=Never \
 -- /bin/sh
```


```bash
# Inside the pod:
while true; do wget -q -O- http://hpa-demo:80; done
```

👉 This loop continuously hits the service, driving CPU usage up.

---

## 7️⃣ Observe Autoscaling

```bash
kubectl get hpa --watch
```

Expected output:
```
NAME       REFERENCE             TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
hpa-demo   Deployment/hpa-demo   80%/50%   1         5         3          3m
```

Pods will scale up as CPU > 50%, and scale down when load stops.

---

## 8️⃣ Cleanup

```bash
kubectl delete namespace hpa-lab
```

---

## 🧠 Explanation for Participants

- **Deployment**: starts with 1 replica, requests 200m CPU.  
- **HPA**: monitors CPU utilization, target = 50%.  
- **Load generator**: increases CPU usage.  
- **Autoscaler**: adds replicas until average CPU < 50%.  
- **Cluster stability**: HPA ensures apps scale up under load and scale down when idle, saving resources.

