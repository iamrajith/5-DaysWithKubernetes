# Kubernetes Blue-Green Deployments
Our critical application requires a swift version upgrade without downtime

## Table of Contents
1. [Introduction to Kubernetes](#introduction-to-kubernetes)
2. [Creating Deployments and Services](#creating-deployments-and-services)
    - [Creating Deployments](#creating-deployments)
    - [Creating Services](#creating-services)
3. [ClusterIP Services](#clusterip-services)
4. [NodePort Services](#nodeport-services)
5. [Comparing Endpoint IPs with Pods](#comparing-endpoint-ips-with-pods)
6. [References](#references)

---

## Summary 
Our application is currently running on version one. The image used is the "httpd: alpine" image. We planned to update it to the latest version using the "nginx: alpine" image. The change needs to be implemented immediately, without any delays. Once the update is complete, the new requests should redirect to the container running with the version 2 "nginx" image. 

## Creating Deployments and Services
Create version 1 ( Blue ) of our application with the deployment and service definition file given here.
### Creating deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: bluegreenv1
  name: blue
spec:
  replicas: 4
  selector:
    matchLabels:
      app: bluegreen
      version: appv1
  template:
    metadata:
      labels:
        app: bluegreen
        version: appv1
    spec:
      containers:
      - image: httpd:alpine
        name: httpd
```

Apply the Deployment:
```
kubectl apply -f nginx-deploymentV1.yaml
```

### Creating Services
Now let's create the Service that points to the bluegreen Deployment:

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: bluegreen
  name: bluegreen
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: Change the port based on your username # 30101
  selector:
    app: bluegreen
    version: v1
  type: NodePort
```

Create the Service:
```
kubectl apply -f nginx-service.yaml
```

### Testing the Deployment and Service
1. Create a busybox pod:
    ```
    kubectl run busybox --image=busybox --restart=Never -- sleep 3600
    ```

2. Execute a curl command to check if the ClusterIP service is listening to the Nginx webpage:
    ```
    kubectl exec -it busybox -- sh
    curl nginx-service
    # Expected output: The HTML content of the Nginx webpage

    ```
3. Use the node's IP address and the specified nodePort (e.g., 30080) to access the Nginx service externally:
    ```
    http://<node_ip>:30080
    ```
### Creating new Deployment bluegreen-v2
Next, let's create a new Deployment bluegreen-v2 which uses image nginx:alpine with 4 replicas. Its Pods should have labels "app: bluegreen" and version: v2

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: bluegreenv2
  name: green
spec:
  replicas: 4
  selector:
    matchLabels:
      app: bluegreen
      version: v2
  template:
    metadata:
      labels:
        app: bluegreen
        version: v2
    spec:
      containers:
      - image: nginx:alpine
        name: nginx
```

Create the Deployment:
```
kubectl apply -f nginx-deploymentV1.yaml
```
### Modify the Service
Now let's modify the Service to points to the bluegreenv2 Deployment:

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: bluegreen
  name: bluegreen
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30080
  selector:
    app: bluegreen
    version: v2
  type: NodePort
  ```
Apply the change:
```
kubectl apply -f nginx-service.yaml
```

### Testing the Deployment and Service
1. Create a busybox pod:
    ```
    kubectl run busybox --image=busybox --restart=Never -- sleep 3600
    ```

2. Execute a curl command to check if the ClusterIP service is listening to the Nginx webpage:
    ```
    kubectl exec -it busybox -- sh
    curl nginx-service
    # Expected output: The HTML content of the Nginx webpage

    ```
3. Use the node's IP address and the specified nodePort (e.g., 30080) to access the Nginx service externally:
    ```
    http://<node_ip>:30080
    ```

## Comparing Endpoint IPs with Pods
- **Endpoints** represent the IP addresses of one or more Pods dynamically assigned to a Service.


1. First, let's assume we have two Pods associated with our Nginx Deployment. We'll use the following Pod names:
   - `nginx-pod-1`
   - `nginx-pod-2`

2. Now, let's retrieve the IP addresses of these Pods using the `kubectl get pods -o wide` command:
   ```shell
   $ kubectl get pods -o wide
   NAME           READY   STATUS    RESTARTS   AGE   IP            NODE
   nginx-pod-1    1/1     Running   0          5m    10.244.1.10   node-1
   nginx-pod-2    1/1     Running   0          5m    10.244.2.20   node-2
   ```

   In this example:
   - `nginx-pod-1` has an IP address of `10.244.1.10`.
   - `nginx-pod-2` has an IP address of `10.244.2.20`.

3. Let's get more information about the `nginx-service` using `kubectl describe`:

```shell
kubectl describe service nginx-service
```

This command will provide detailed information about the service, including its IP address, ports, and associated endpoints.


4. Next, let's check the Endpoints associated with our `nginx-service` using the `kubectl get endpoints nginx-service` command:
   ```shell
   $ kubectl get endpoints nginx-service
   NAME            ENDPOINTS                      AGE
   nginx-service   10.244.1.10:80,10.244.2.20:80   5m
   ```

   Here, the `nginx-service` has endpoints corresponding to both Pods:
   - `10.244.1.10:80` (associated with `nginx-pod-1`)
   - `10.244.2.20:80` (associated with `nginx-pod-2`)

5. Finally, we can compare the IP addresses from the Endpoints with the Pod IPs to verify that they match.

Remember that these IP addresses are internal to the cluster and are used for communication between services and Pods. The Service abstraction ensures seamless connectivity without exposing individual Pod IPs externally.
## References
- [Kubernetes Documentation](https://kubernetes.io/docs/concepts/services-networking/service/)
- [My Blog](https://rajith.in/)
