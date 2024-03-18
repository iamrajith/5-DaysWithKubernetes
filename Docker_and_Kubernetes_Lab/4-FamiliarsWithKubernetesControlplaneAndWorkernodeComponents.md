
# Explore Kubernetes Control Plane and Worker Node Components

## Table of Contents

1. [Introduction](#introduction)
2. [Listing All Control Plane Components](#listing-all-control-plane-components)
3. [Explore Individual Control Plane Components](#exploring-individual-control-plane-components)
4. [Explore Worker Node Components](#exploring-worker-node-components)
5. [References](#references)

## Introduction

So far we have gone through the basics of docker. Let's dive into Kubernetes by exploring the control plane and worker node components.

## Listing All Control Plane Components

The control plane components run in the 'kube-system' namespace. To list all pods in the 'kube-system' namespace, use the following command:

```bash
kubectl get pods -n kube-system
```

This lists all the control plane components.

## Exploring Individual Control Plane Components

We can obtain a detailed description of each pod. Let's see how to do it for each control plane component:

### API Server

```bash
kubectl -n kube-system describe pod -l component=kube-apiserver
```

### etcd

```bash
kubectl -n kube-system describe pod -l component=etcd
```

### Scheduler

```bash
kubectl -n kube-system describe pod -l component=kube-scheduler
```

### Controller Manager

```bash
kubectl -n kube-system describe pod -l component=kube-controller-manager
```

### DNS (CoreDNS or kube-dns)

```bash
kubectl -n kube-system describe pod -l k8s-app=kube-dns
```

## Exploring Worker Node Components

Worker nodes run the workloads and communicate with the control plane through the Kubernetes API. The main components of a worker node are:

- **Kubelet**: This is the primary node agent that communicates with the API server to ensure that containers are running in a pod.

`Get the status of kubelet`
```bash
systemctl status kubelet
```

- **Kube-proxy**: This is a network proxy that maintains network rules on the node. These network rules allow network communication to your Pods from network sessions inside or outside of your cluster.

`Get information about kube-proxy`
```bash
kubectl -n kube-system get pod -o wide | grep kube-proxy
```

- **Container Runtime**: This is the software that is responsible for running containers. Kubernetes supports several runtimes: Docker, containerd, CRI-O, and any implementation of the Kubernetes CRI (Container Runtime Interface).

`Get the status of containerd`
```bash
systemctl status containerd
```

## References

1. [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/)
2. [Kubernetes Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
3. [Rajith's Kubernetes Guide](https://www.rajith.in/Kubernetes/)
