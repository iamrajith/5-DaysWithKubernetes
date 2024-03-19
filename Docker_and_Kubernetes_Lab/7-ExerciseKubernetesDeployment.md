# Exercise Kubernetes Deployment

## Table of Contents
1. [Introduction](#introduction)
2. [Imperative vs. Declarative](#imperative-vs-declarative)
3. [Creating the Deployment](#creating-the-deployment)
    - 3.1 [Declarative Method](#declarative-method)
    - 3.2 [Imperative Method](#imperative-method)
4. [Verifying Deployment](#verifying-deployment)
    - 4.1 [Get Deployment Details](#get-deployment-details)
    - 4.2 [ReplicaSet and Pods](#replicaset-and-pods)
5. [Scaling and Upgrading](#scaling-and-upgrading)
    - 5.1 [Scale Pods](#scale-pods)
    - 5.2 [Rollout Status](#rollout-status)
    - 5.3 [Upgrade Image Version](#upgrade-image-version)
    - 5.4 [Scale Down](#scale-down)
6. [Deleting Deployment](#deleting-deployment)
    - 6.1 [Using Imperative Command](#using-imperative-command)
    - 6.2 [Using Definition File](#using-definition-file)
7. [References](#references)
8. [Deployment Definition File](#deployment-definition-file)

## 1. Introduction
In this lab exercise, we'll cover essential Kubernetes deployment tasks. You'll create a deployment, verify its details, scale the number of pods, upgrade the application image, and finally, delete the deployment.

## 2. Imperative vs. Declarative
- **Imperative**: Directly create, update, or delete resources using commands.
- **Declarative**: Describe desired resource states using configuration files (YAML) and apply them.

## 3. Creating the Deployment
### 3.1 Declarative Method

1. Create a YAML specification for the deployment:
   - Name: `my-deployment`
   - Container: `my-container` with image `nginx:1.14.2`
   - Number of replicas: `3`

2. Create a file named `deployment.yaml`:

```bash
vi deployment.yaml
```

Add the following content to `deployment.yaml`:

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
*(Note: This file describes the desired state of your application.)*

[References](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment)

### 3.2 Imperative Method
Alternatively, you can create the same deployment imperatively using the following command:

```bash
kubectl create deployment my-deployment --image=nginx --replicas=3
```
This command directly creates the deployment.

*(Note: This command may fail since the deployment named `my-deployment` already exist. Either change the name of the deployment or delete the existing deployment then recreate it with Imperative Method.)*
*(To delete deployment refer the session ## 6. Deleting Deployment)*[Deleting Deployment](#deleting-deployment)


## 4. Verifying Deployment
### 4.1 Get Deployment Details
- To list all deployments:
  ```bash
  kubectl get deployments
  ```
- To describe a specific deployment (e.g., "my-deployment"):
  ```bash
  kubectl describe deployment my-deployment
  ```

### 4.2 ReplicaSet and Pods
- A deployment automatically creates a ReplicaSet, which manages identical Pods.
- To view ReplicaSets:
  ```bash
  kubectl get rs
  ```
- To see Pods under a specific deployment:
  ```bash
  kubectl get pods -o wide
  ```

## 5. Scaling and Upgrading
### 5.1 Scale Pods
- To scale the number of Pods in a deployment (e.g., "my-deployment") to 5:
  ```bash
  kubectl scale deployment my-deployment --replicas=5
  ```

### 5.2 Rollout Status
- To verify the rollout status:
  ```bash
  kubectl rollout status deployment my-deployment
  ```

### 5.3 Upgrade Image Version
1. Update the image version in your deployment YAML file.
2. Apply the changes:
   ```bash
   kubectl apply -f deployment.yaml
   ```
   *(Note: Image upgrades will be explained in the next exercise.)*

### 5.4 Scale Down
- To scale down to 3 replica:
  ```bash
  kubectl scale deployment my-deployment --replicas=3
  ```
- To scale down to 1 replica:
  ```bash
  kubectl scale deployment my-deployment --replicas=1
  ```

## 6. Deleting Deployment
### 6.1 Using Imperative Command
- To delete a deployment (e.g., "my-deployment"):
  ```bash
  kubectl delete deployment my-deployment
  ```

### 6.2 Using Definition File
- To delete the deployment using the definition file (`deployment.yaml`):
  ```bash
  kubectl delete -f deployment.yaml
  ```

## 7. References
- [Kubernetes Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [My Blog](https://www.rajith.in/Kubernetes/KubernetesPart5_Deployment-1/)
