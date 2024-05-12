

Let's walk through the Kubernetes deployment lab exercise with the specified scenarios. We'll create a Deployment, update its version, modify the rollout strategy, and observe how pods behave during the process.

## Table of Contents
1. [Create a Deployment with the Default Strategy (Rolling Update)](#create-a-deployment-with-the-default-strategy-rolling-update)
2. [Update the Version of the Deployment](#update-the-version-of-the-deployment)
3. [Modify the Rollout Strategy to Recreate](#modify-the-rollout-strategy-to-recreate)
4. [Change the Image Used for the Deployment](#change-the-image-used-for-the-deployment)
5. [Check Pods During Recreation](#check-pods-during-recreation)
6. [View Deployment YAML Output](#view-deployment-yaml-output)

1. **Create a Deployment with the Default Strategy (Rolling Update):**
   - Create a YAML file (e.g., `my-deployment.yaml`) with the following content:
     ```yaml
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: my-deployment
     spec:
       replicas: 3
       selector:
         matchLabels:
           app: my-app
       template:
         metadata:
           labels:
             app: my-app
         spec:
           containers:
             - name: my-container
               image: nginx:1.19
     ```
   - Apply the deployment using `kubectl apply -f my-deployment.yaml`.

2. **Update the Version of the Deployment:**
   - Edit the `my-deployment.yaml` file and change the `image` to a newer version (e.g., `nginx:1.20`).
   - Apply the updated deployment using `kubectl apply -f my-deployment.yaml`.
   - Observe how the pods are recreated one by one with the new image.

3. **Modify the Rollout Strategy to Recreate:**
Here's the updated `my-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:1.20
  strategy:
    type: Recreate
```

After applying this updated YAML, you can verify the changes using the following commands:

1. Apply the updated deployment:
   ```
   kubectl apply -f my-deployment.yaml
   ```

2. Check the status of the deployment:
   ```
   kubectl get deployment my-deployment
   ```

3. Observe how the pods are recreated using the `Recreate` strategy:
   ```
   kubectl get deployment my-deployment -o yaml 
   ```

The `Recreate` strategy ensures that all old pods are terminated before new ones are created. 

4. **Change the Image Used for the Deployment:**
   - Edit the `my-deployment.yaml` file and update the `image` to a different one (e.g., `nginx:1.21`).
   - Apply the updated deployment using `kubectl apply -f my-deployment.yaml`.
   - Observe how the pods are recreated using the new image.

5. **Check Pods During Recreation:**
   - Run `kubectl get pods -l app=my-app -w` to watch the pods.
   - Notice how the old version of the pod is deleted immediately, without waiting for the new pods to create.

6. **View Deployment YAML Output:**
   - Run `kubectl get deployment my-deployment -o yaml` to verify that the changes were successful.

Remember to adjust the YAML file names and labels according to your environment. This exercise will help you understand deployment strategies and how Kubernetes handles rolling updates and recreates pods. 😊
