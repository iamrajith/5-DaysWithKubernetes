# 🧑‍💻 Kubernetes Ingress Training Lab

## 📌 Lab Goal
Each participant will:
- Work in their own namespace (`ingress-labXX`)  
- Deploy sample NGINX applications  
- Expose them via Services  
- Create an Ingress resource with **host‑based and path‑based routing**  
- Access the apps externally through the shared Azure Load Balancer IP  

---

## 🛠️ Step 1: Namespace Setup
Each participant gets a namespace based on their ID (`user01`, `user02`, etc.):

*(Replace `XX` with your participant number)*

```bash
kubectl create namespace ingress-labXX 
```
```bash
kubectl config set-context --current --namespace=ingress-labXX
```

# Validate you are in your own namespace.
```bash
kubectl config view --minify | grep namespace:
```


---

```bash
vi app-deploy.yaml
```
## 📂 Step 2: Deploy Application (`app-deploy.yaml`)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app01
  template:
    metadata:
      labels:
        app: app01
    spec:
      containers:
      - name: app01
        image: nginx
        ports:
        - containerPort: 80
```

Apply:
```bash
kubectl apply -f app-deploy.yaml
```

---

## 🌐 Step 3: Service (`app-service.yaml`)

```bash
vi app-service.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: app01
  ports:
  - port: 80
    targetPort: 80
```

Apply:
```bash
kubectl apply -f app-service.yaml
```

---

## 🛠️ Step 4: Ingress Resource (`app-ingress.yaml`)

```bash
vi app-ingress.yaml
```
Each participant uses a **unique host** (`appXX.lab`) to avoid conflicts:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  ingressClassName: nginx
  rules:
  - host: appXX.lab   # Replace `XX` with your participant number
    http:
      paths:
      - path: /app2(/|$)(.*)
        pathType: ImplementationSpecific
        backend:
          service:
            name: app2-service
            port:
              number: 80

      - path: /app3(/|$)(.*)
        pathType: ImplementationSpecific
        backend:
          service:
            name: app3-service
            port:
              number: 80

      - path: /
        pathType: Prefix
        backend:
          service:
            name: app-service
            port:
              number: 80
```

Apply:
```bash
kubectl apply -f app-ingress.yaml
```

---

## 🛠️ Step 5: Deploy Additional Apps

```bash
vi app2-deploy.yaml
```

### `app2-deploy.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app2
  template:
    metadata:
      labels:
        app: app2
    spec:
      containers:
      - name: app2
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: app2-service
spec:
  selector:
    app: app2
  ports:
  - port: 80
    targetPort: 80
```

```bash
vi app3-deploy.yaml
```
### `app3-deploy.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app3-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app3
  template:
    metadata:
      labels:
        app: app3
    spec:
      containers:
      - name: app3
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: app3-service
spec:
  selector:
    app: app3
  ports:
  - port: 80
    targetPort: 80
```

Apply:
```bash
kubectl apply -f app2-deploy.yaml
kubectl apply -f app3-deploy.yaml
```

---

# 🌐 Step 6: Map Hostnames to Azure Load Balancer IP

Ingress requires the correct **Host header**.  
To avoid using `curl -H`, map `appXX.lab` to the Azure Load Balancer IP locally.

---

## 🖥️ Windows
1. Open **Notepad** as Administrator.  
2. Open file:  
   `C:\Windows\System32\drivers\etc\hosts`  
   *(Change file type filter to “All Files”)*  
3. Add entry:  
   ```
   <AzureLoadBalancerIP> appXX.lab
   ```
   *(Replace with your assigned host: `app02.lab`, `app03.lab`, etc.)*  
4. Save file.  
5. Test in browser:  
   - Open Chrome/Edge → `http://appXX.lab`  
   - Expected: NGINX welcome page.

---

## 🍏 macOS
1. Open Terminal.  
2. Edit hosts file:  
   ```bash
   sudo vi  /etc/hosts
   ```
3. Add entry:  
   ```
   <AzureLoadBalancerIP> appXX.lab
   ```
4. Save (`Ctrl+O`, Enter) and exit (`Ctrl+X`).  
5. Test in Safari/Chrome:  
   - Navigate to `http://appXX.lab`.

---

## 🐧 Ubuntu/Linux
1. Open Terminal.  
2. Edit hosts file:  
   ```bash
   sudo vi /etc/hosts
   ```
3. Add entry:  
   ```
   <AzureLoadBalancerIP> appXX.lab
   ```
4. Save and exit.  
5. Test in Firefox/Chrome:  
   - Navigate to `http://appXX.lab`.

---

## 🔍 Curl Validation (Optional)
```bash
curl http://appXX.lab
```

Expected: HTML of the NGINX welcome page.

---

## ✅ Verification Checklist
- Namespace created and active  
- Deployments and Services running  
- Ingress applied with unique host (`appXX.lab`)  
- Hosts file updated with Azure Load Balancer IP  
- Browser test successful  

---

## 💡 Discussion Points
- **Multi‑tenant setup** → Each participant in their own namespace  
- **Unique hostnames** → Prevent routing conflicts (`app01.lab`, `app02.lab`, etc.)  
- **Path‑based routing** → `/app2`, `/app3` under same host  
- **Validation commands** → `kubectl get`, `kubectl describe`, `curl`, browser test  

---