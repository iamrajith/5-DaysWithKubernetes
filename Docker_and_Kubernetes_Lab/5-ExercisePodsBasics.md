
# Exercise Pods Basics and Multi-Container Pods


## Table of Contents
1. [Introduction](#introduction)
2. [Task 1: Nginx Pod](#task-1-nginx-pod)
3. [Task 2: Busybox Pod](#task-2-busybox-pod)
4. [Task 3: Multicontainer Pod](#task-3-multicontainer-pod)
5. [Fields in a Pod Definition File](#fields-in-a-pod-definition-file)
6. [Multi-Container Pods](#multi-container-pods)
7. [Lab Exercise](#lab-exercise)
8. [References](#references)

---

## Introduction

In this lab, let us explore the basics of Kubernetes Pods, including how to create single-container Pods and multicontainer Pods. We will also cover the key fields in a Pod definition file.

### Task 1: Nginx Pod
1. Create a YAML specification for an Nginx Pod:
   - Name: `nginx-pod`
   - Container: `nginx` with image `nginx:1.14.2`

Create a file named `nginx-pod.yaml` using the `vi` editor:

```bash
vi nginx-pod.yaml
```

Add the following content to `nginx-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx:1.14.2
```

Apply the Pod configuration using the following command:

```bash
kubectl apply -f nginx-pod.yaml
```

Check the status of the Pod:

```bash
kubectl get pod nginx-pod
```

### Task 2: Busybox Pod
1. Create a YAML specification for a Busybox Pod:
   - Name: `busybox-pod`
   - Container: `busybox` with image `busybox:1.33.1`
   - Command: Run a simple sleep command (e.g., `sleep 3600`)

Create a file named `busybox-pod.yaml` using the `vi` editor:

```bash
vi busybox-pod.yaml
```

Add the following content to `busybox-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox-pod
spec:
  containers:
    - name: busybox
      image: busybox:1.33.1
      command: ["sleep", "3600"]
```

Apply the Pod configuration using the following command:

```bash
kubectl apply -f busybox-pod.yaml
```

Check the status of the Pod:

```bash
kubectl get pod busybox-pod
```

### Task 3: Multicontainer Pod
1. Create a YAML specification for a multicontainer Pod:
   - Name: `multicontainer-pod`
   - Containers:
     - `nginx-container` with image `nginx:1.14.2`
     - `busybox-container` with image `busybox:1.33.1`
     - Command: Run a simple sleep command (e.g., `sleep 3600`) in the `busybox-container`

Create a file named `multicontainer-pod.yaml` using the `vi` editor:

```bash
vi multicontainer-pod.yaml
```

Add the following content to `multicontainer-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multicontainer-pod
spec:
  containers:
    - name: nginx-container
      image: nginx:1.14.2
    - name: busybox-container
      image: busybox:1.33.1
      command: ["sleep", "3600"]
```

Apply the Pod configuration using the following command:

```bash
kubectl apply -f multicontainer-pod.yaml
```

Check the status of the Pod:

```bash
kubectl get pod multicontainer-pod
```

## Fields in a Pod Definition File

1. **apiVersion**: Specifies the version of the Kubernetes API to use.
2. **kind**: Indicates the type of resource (e.g., "Pod").
3. **metadata**: Contains information about the Pod, such as its name, labels, and annotations.
4. **spec**: Describes the desired state of the Pod, including the containers, volumes, and other settings.

For detailed information, refer to the official Kubernetes documentation on [Pods](https://kubernetes.io/docs/concepts/workloads/pods/).

For more insights on this topic, have a look on my blog post on [Walking Through Pods](https://www.rajith.in/Kubernetes/WalkingThroughThePodsPart1/).

---

## Multi-Container Pods

A **multi-container Pod** in Kubernetes is a Pod that contains two or more related containers. These containers share resources such as network space, shared volumes, and work together as a single unit. Multi-container Pods are useful for scenarios where tightly coupled processes need to run together or when containers must share resources within the same logical host.


## References
- [Kubernetes Pods Documentation](https://kubernetes.io/docs/concepts/workloads/pods/)
- [Rajith's Blog on Walking Through Pods](https://www.rajith.in/Kubernetes/WalkingThroughThePodsPart1/)