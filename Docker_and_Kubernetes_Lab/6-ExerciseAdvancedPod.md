# Exercise: Advanced Pod Commands and Details

Discover the full potential of pod commands and master their effective use. Begin your journey of exploration, let us start!

## Table of Contents
1. [Introduction](#introduction)
2. [Describe a Pod](#describe-a-pod)
3. [Get Logs from a Container in a Pod](#get-logs-from-a-container-in-a-pod)
4. [Execute a Command in a Container](#execute-a-command-in-a-container)
5. [List All Pods in All Namespaces](#list-all-pods-in-all-namespaces)
6. [List Pods with Additional Details](#list-pods-with-additional-details)
7. [Get Pod YAML](#get-pod-yaml)
8. [References](#references)

---

## Introduction
In this document, we'll explore advanced `kubectl` commands related to Kubernetes Pods. These commands provide detailed information and allow you to interact with Pods effectively.

## Describe a Pod
The `kubectl describe` command provides detailed information about a specific Pod, including events, labels, and annotations. To describe a Pod, use the following syntax:
```bash
kubectl describe pod <pod-name>
```
This command will give you insights into the Pod's configuration, status, and related resources.

## Get Logs from a Container in a Pod
To retrieve logs from a specific container within a Pod, use the `kubectl logs` command. For example:
```bash
kubectl logs <pod-name> -c <container-name>
```
Replace `<pod-name>` and `<container-name>` with actual values relevant to your environment. This command is useful for debugging and monitoring containerized applications.

## Execute a Command in a Container
You can run a command inside a specific container within a Pod using the `kubectl exec` command. For example:
```bash
kubectl exec -it <pod-name> -c <container-name> -- <command>
```
This allows you to interactively execute commands within the container. Replace placeholders with actual values as needed.

## List All Pods in All Namespaces
To view all Pods across all namespaces, use the following command:
```bash
kubectl get pods --all-namespaces
```

## List Pods with Additional Details
To display additional details about Pods (such as node name), use:
```bash
kubectl get pods -o wide
```

## Get Pod YAML
To obtain the YAML definition of a specific Pod (e.g., `my-pod`), execute:
```bash
kubectl get pod my-pod -o yaml
```

## References
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)