# Let's explore the concept of Canary deployment



We have an existing application. That runs on the "httpd: alpine" image. Now, let's switch to the "nginx: alpine" image. The change should take place gradually. Let us make use of the Canary deployment approach here.

First, create a new Deployment called deploymentCanary-v2 and configure it to use the "nginx: alpine" image.

Next, set up traffic splitting so that 20% of incoming requests go to the new "Nginx: alpine" version. The remaining 80% continue to hit the old "httpd: alpine" version.

Monitor the behavior of both versions while ensuring that the total number of Pods across both Deployments remains at 10.

This gradual transition allows us to test the new image without disrupting the system. It's like experimenting in a controlled lab environment! 


---


## Creating Deployments and Services
Create version 1 of our application with the deployment and service definition file given here.
### Creating deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: deploymentCanary
  name: deploymentcanary-v1
spec:
  replicas: 10
  selector:
    matchLabels:
      app: deploymentCanary
  template:
    metadata:
      labels:
        app: deploymentCanary
    spec:
      containers:
      - image: httpd:alpine
        name: httpd
```

Apply the Deployment:
```
kubectl create -f nginx-deploymentV1.yaml
```

### Creating Services
Now let's create the Service that points to the Deployment:

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: deploymentCanary
  name: servicecanary
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: Change the port based on your username # 30101
  selector:
    app: deploymentCanary
  type: NodePort
```

Create the Service:
```
kubectl create -f nginx-service.yaml
```
Here we have the "NodePort" Service named "serviceCanary". It has the Pods of Deployment of the app "deploymentCanary" as the selector.

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
### Reduce the replicas of the old deployment to 8
Change the number of replicas to 8.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: deploymentCanary
  name: deploymentcanary-v1
spec:
  replicas: 8
  selector:
    matchLabels:
      app: deploymentCanary
  template:
    metadata:
      labels:
        app: deploymentCanary
    spec:
      containers:
      - image: httpd:alpine
        name: httpd
```

Create the Deployment:
```
kubectl apply -f nginx-deploymentV1.yaml
```
### Create a new Deployment with the same label

Now, both deployments will point to the same service.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: deploymentCanary
  name: deploymentcanary-v2
spec:
  replicas: 2
  selector:
    matchLabels:
      app: deploymentCanary
  template:
    metadata:
      labels:
        app: deploymentCanary
    spec:
      containers:
      - image: nginx:alpine
        name: nginx
  ```
Apply the change:
```
kubectl create  -f nginx-service.yaml
```

### Testing the Deployment and Service
1. Create a busybox pod:
    ```
    kubectl run ubuntu --image=ubuntu --restart=Never -- sleep 3600
    ```

2. Execute a curl command to check if the ClusterIP service is listening to the Nginx webpage:
    ```
    kubectl exec -it ubuntu -- sh
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