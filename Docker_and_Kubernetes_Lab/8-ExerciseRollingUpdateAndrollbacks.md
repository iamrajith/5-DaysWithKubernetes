# Advanced Kubernetes Deployment Exercise

## Table of Contents
1. [Introduction](#introduction)
2. [Creating a Deployment](#creating-a-deployment)
    - 2.1 [Using the Command Line](#using-the-command-line)
    - 2.2 [Using a YAML Definition File](#using-a-yaml-definition-file)
3. [Verifying Deployment](#verifying-deployment)
    - 3.1 [Deployment Details](#deployment-details)
    - 3.2 [Scaling Events](#scaling-events)
4. [Updating a Deployment](#updating-a-deployment)
    - 4.1 [Using `kubectl set image`](#using-kubectl-set-image)
    - 4.2 [Using `kubectl edit deployment`](#using-kubectl-edit-deployment)
    - 4.3 [Editing the YAML file](#editing-the-yaml-file)
5. [Rolling Back a Deployment](#rolling-back-a-deployment)
6. [Pausing and Resuming a Deployment](#pausing-and-resuming-a-deployment)
7. [References](#references)
8. [Deployment Definition Files](#deployment-definition-files)

## 1. Introduction
In this Kubernetes deployment exercise, let us learn to update deployments, event monitoring, and perform rollbacks.

## 2. Creating a Deployment
### 2.1 Using the Command Line
To create a deployment using the command line, run the following command:

```bash
kubectl create deployment my-deployment --image=nginx --replicas=3
```

### 2.2 Using a YAML Definition File
Alternatively, create the same deployment using the following YAML definition file (`my-deployment.yaml`):

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
          image: nginx:1.14.2
          ports:
            - containerPort: 80
```

Apply the deployment using:

```bash
kubectl apply -f my-deployment.yaml
```

## 3. Verifying Deployment
### 3.1 Deployment Details
Check the status of the deployment:

```bash
kubectl describe deployment my-deployment
```

Observe the number of replicas in the current and new ReplicaSets.

### 3.2 Scaling Events
Watch the events related to the deployment while updating the image:

```bash
kubectl get events --watch
```

You'll see events indicating the rolling update process.

## 4. Updating a Deployment

We'll explore three methods for updating deployments: 

### 4.1 Using `kubectl set image`
We can use the `kubectl set image` command to update the image of the nginx deployment. For example, to update the nginx container image to version 1.23, run the following command:

```bash
kubectl set image deployment/nginx nginx=nginx:1.23
```

### 4.2 Using `kubectl edit deployment`

1. **Open the Deployment YAML File**:
   - Use the following command to open the YAML configuration of the deployment in your default editor:
     ```bash
     kubectl edit deployment my-deployment
     ```
   - This command will open the deployment configuration for `my-deployment`.

2. **Make Changes**:
   - Navigate to the `spec.template.spec.containers` section.
   - Update the image version or any other desired fields.
   - Save the file and exit the editor.
   - It automatically apply the changes to the configuration. You can watch the changes with the `kubectl describe` command.


### 4.3 Editing the YAML file

1. **Open the deployment YAML file for editing**:

    ```bash
    vi my-deployment.yaml
    ```
2. **Make Changes**:

   - Navigate to the `spec.template.spec.containers` section.
   - Update the image version.
   - Save the file and exit the editor.
   - 
```yaml
    spec:
      containers:
        - name: my-container
          image: nginx:1.14.2  -->   change it to nginx:1.16.1
          ports:
            - containerPort: 80
```
3. **Apply the Changes**:
   - The changes made in the YAML file will be applied to the deployment using the following command:
     ```bash
     kubectl apply -f my-deployment.yaml
    ```

## 5. Rolling Back a Deployment
### 5.1 Check Rollout History
View the rollout history of your deployment:

```bash
kubectl rollout history deployment/my-deployment
```

### 5.2 Roll Back to Previous Revision
To roll back to a previous revision:

```bash
kubectl rollout undo deployment/my-deployment
```

## 6. Pausing and Resuming a Deployment
Pause a deployment to apply fixes:

```bash
kubectl rollout pause deployment/my-deployment
```

Resume the rollout:

```bash
kubectl rollout resume deployment/my-deployment
```

## 7. References
- [Kubernetes Documentation](https://kubernetes.io/)
- [Kubernetes Rolling Update](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/)
- [Kubernetes Canary Deployments](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/#canary-deployments)
