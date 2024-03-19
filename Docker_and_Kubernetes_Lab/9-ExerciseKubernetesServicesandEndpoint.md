# Kubernetes Services and Endpoint

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

## Introduction to Kubernetes
Kubernetes, also known as "k8s," is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications across a cluster of nodes¹². It provides a powerful framework for managing workloads, networking, and services within a distributed environment.

## Creating Deployments and Services
### Creating Deployments
In Kubernetes, a **Deployment** defines a desired state for a set of Pods. Let's create a Deployment running Nginx:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
```

Apply the Deployment:
```
kubectl apply -f nginx-deployment.yaml
```

### Creating Services
Now let's create a **ClusterIP Service** that points to the Nginx Deployment:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

Apply the Service:
```
kubectl apply -f nginx-service.yaml
```

### Testing ClusterIP Service
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

3. Delete the busybox pod:
    ```
    kubectl delete pod busybox
    ```

### Creating NodePort Service
Next, let's create a **NodePort Service**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  # Specify the nodePort (e.g., 30080) to expose the service externally
  nodePort: 30080
```

Apply the NodePort Service:
```
kubectl apply -f nginx-nodeport.yaml
```

### Testing NodePort Service
1. Get the IP address of any node in your cluster:
    ```
    kubectl get nodes -o wide
    ```

2. Use the node's IP address and the specified nodePort (e.g., 30080) to access the Nginx service externally:
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
