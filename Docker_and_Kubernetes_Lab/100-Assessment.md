# Kubernetes Practical Assessment

> **Objective:** Complete all tasks using Kubernetes CLI commands and demonstrate the required outputs and application behavior.

---

## 1. Namespace Creation

Create a namespace named:

```text
assessmentXX
```

Configure your current Kubernetes context to use this namespace.

### Required evidence

Show the output of:

```bash
kubectl config view --minify | grep namespace
```

---

## 2. Deployment Creation

Within the `assessmentXX` namespace, create a Deployment with the following specifications:

| Requirement     | Value            |
| --------------- | ---------------- |
| Deployment name | `mydeployment01` |
| Image           | `nginx:latest`   |
| Replicas        | `3`              |

### Required evidence

Capture the output of:

```bash
kubectl get deployment
```

and:

```bash
kubectl get rs
```

---

## 3. Service Exposure

Expose the Deployment externally using a **NodePort Service**.

### Requirements

* The Service must expose the `mydeployment01` Deployment.
* Use the **NodePort assigned to you based on your username**.
* Verify that the application is accessible externally through the assigned NodePort.

### Required evidence

Capture the output of:

```bash
kubectl get pod -o wide
```

and:

```bash
kubectl describe svc
```

---

## 4. Update the Deployment

Update the Deployment to use the following image:

```text
caddy:latest
```

Verify that the Deployment is successfully updated.

Once the update is complete:

1. Refresh the application in the browser.
2. Verify that the application is running using the new image.
3. Capture a screenshot of the application.

---

## 5. Configure Auto Scaling

Configure a **Horizontal Pod Autoscaler (HPA)** for the Deployment.

Use the following configuration:

| Configuration             | Value |
| ------------------------- | ----- |
| CPU utilization threshold | `80%` |
| Minimum replicas          | `3`   |
| Maximum replicas          | `8`   |

Verify that the HPA has been created successfully.

### Required evidence

Show the HPA configuration and status output.

---

## 6. Rollback the Deployment

Rollback the Deployment to the previous version.

### Required evidence

Capture the output of:

```bash
kubectl rollout history
```

Also show the details of **each Deployment revision**.

---

# Submission Checklist

Before completing the assessment, verify that you have captured the required evidence for all tasks:

* [ ] Namespace created and configured
* [ ] Deployment created with 3 replicas
* [ ] ReplicaSet output captured
* [ ] NodePort Service created and verified
* [ ] Pod and Service details captured
* [ ] Deployment updated to `caddy:latest`
* [ ] Browser screenshot captured after the update
* [ ] HPA configured with the required values
* [ ] HPA status/configuration output captured
* [ ] Deployment rollback completed
* [ ] Rollout history and revision details captured


