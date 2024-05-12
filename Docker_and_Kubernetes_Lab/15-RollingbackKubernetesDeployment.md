# Rolling Back a Kubernetes Deployment (Using Nginx)

Let's create a more comprehensive lab exercise for rolling back a Kubernetes Deployment using an Nginx image. 

## Table of Contents
1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Step 1: Create a Deployment](#step-1-create-a-deployment)
4. [Step 2: Change Revision History](#step-2-change-revision-history)
5. [Step 3: Update the Version ](#step-3-update-the-version)
6. [Step 4: Check Rollout History](#step-4-check-rollout-history)
7. [Step 5: Roll Back to a Previous Revision](#step-5-roll-back-to-a-previous-revision)
8. [Step 6: Verify Rollback Success](#step-6-verify-rollback-success)
9. [Conclusion](#conclusion)

---

## Introduction
In this lab exercise, we'll walk through the process of rolling back a Kubernetes Deployment using an Nginx image. We'll create a Deployment, update its version, and then perform a rollback to a previous revision.


## Step 1: Create a Deployment
1. Create a Deployment with three replicas using the Nginx image (nginx:1.13.0). Below is an example of a Deployment YAML definition:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
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
          image: nginx:1.13.0
```

Apply the definition using:

```bash
kubectl apply -f nginx-deployment.yaml
```

---

## Step 2: Change Revision History
Update the Deployment definition file (`nginx-deployment.yaml`) to set the revision history limit to 10. Add the following field under the `spec` section:

```yaml
spec:
  revisionHistoryLimit: 10
```

Apply the updated Deployment:

```bash
kubectl apply -f nginx-deployment.yaml --record
```

---

## Step 3: Update the Version
1. Change the image version in `nginx-deployment.yaml` to a new version (e.g., `nginx:2.0.0`).

We'll start with version `nginx:1.14.2`, apply the change, then proceed to version `nginx:1.16.1`. We'll focus on the container spec section in the exercise.

---

###  Update to Nginx 1.14.2
1. Open your existing `nginx-deployment.yaml` file.
2. Locate the `containers` section under the `spec` of your Deployment.
3. Update the `image` field to use `nginx:1.14.2`:

   ```yaml
   spec:
     replicas: 3
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
             image: nginx:1.14.2
             ports:
               - containerPort: 80
   ```

4. Apply the updated Deployment:

   ```bash
   kubectl apply -f nginx-deployment.yaml --record
   ```

5. Observe the Pods being created and verify that they are running the specified Nginx version.

### Update to Nginx 1.16.1
1. Open the same `nginx-deployment.yaml` file.
2. Modify the `image` field to use `nginx:1.16.1`:

   ```yaml
   spec:
     containers:
       - name: nginx
         image: nginx:1.16.1
         ports:
           - containerPort: 80
   ```

3. Apply the updated Deployment:

   ```bash
   kubectl apply -f nginx-deployment.yaml --record
   ```

4. Again, observe the Pods as they roll out with the new Nginx version.

---

Remember to adjust the Deployment name and other details according to your environment. 

Wait for the Pods to roll out and become healthy.

### Try for some other version too

Here are the other version of nginx, you can try changing the version to have number of revision history.

nginx:1.23.4
nginx:1.23.2
nginx:1.23
nginx:1.19.6

---

## Step 4: Check Rollout History
To view the rollout history, run:

```bash
kubectl rollout history deploy nginx-deployment
```

This command will display the revision history along with any change causes.

---

## Step 5: Roll Back to a Previous Revision
To undo the current rollout and rollback to the previous revision, use:

```bash
kubectl rollout undo deploy nginx-deployment
```

Alternatively, you can specify a specific revision to roll back to using `--to-revision`:

```bash
kubectl rollout undo deploy nginx-deployment --to-revision=<revision-number>
```

---

## Step 6: Verify Rollback Success
After rolling back, check if the Deployment is running as expected:

```bash
kubectl describe deploy nginx-deployment
```

Ensure that the Pods are healthy and the desired version (e.g., `nginx:1.13.0`) is deployed.

---

## Conclusion
Rollbacks are essential for maintaining service stability. By following these steps, you can confidently manage deployments and handle rollbacks effectively.

---

Remember to replace `<revision-number>` with the actual revision you want to roll back to. Adjust the version numbers in your Deployment definition file as needed.

