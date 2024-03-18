# Advanced Kubernetes Deployment Exercise

## Table of Contents
1. [Introduction](#introduction)
2. [Creating a Deployment](#creating-a-deployment)
    - 2.1 [Using the Command Line](#using-the-command-line)
    - 2.2 [Using a YAML Definition File](#using-a-yaml-definition-file)
3. [Verifying Deployment](#verifying-deployment)
    - 3.1 [Deployment Details](#deployment-details)
    - 3.2 [Scaling Events](#scaling-events)
4. [Rolling Back a Deployment](#rolling-back-a-deployment)
5. [Pausing and Resuming a Deployment](#pausing-and-resuming-a-deployment)
6. [References](#references)

## 1. Introduction
In this advanced Kubernetes deployment exercise, we'll explore update deployments, monitor events, and perform rollbacks.

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

## 4. Rolling Back a Deployment
### 4.1 Check Rollout History
View the rollout history of your deployment:

```bash
kubectl rollout history deployment/my-deployment
```

### 4.2 Roll Back to Previous Revision
To roll back to a previous revision:

```bash
kubectl rollout undo deployment/my-deployment
```

## 5. Pausing and Resuming a Deployment
Pause a deployment to apply fixes:

```bash
kubectl rollout pause deployment/my-deployment
```

Resume the rollout:

```bash
kubectl rollout resume deployment/my-deployment
```

## 6. References
- [Kubernetes Documentation](https://kubernetes.io/)
- [Kubernetes Rolling Update](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/)



