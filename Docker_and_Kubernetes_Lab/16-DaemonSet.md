# DaemonSet 

A **DaemonSet** ensures that all (or some) nodes run a copy of a Pod. 
If there is a node addition, then the pod in the DaemonSet will automatically scheduled to that node. The same goes for deleting the nodes; the pod will not be rescheduled but also deleted.


Here are some typical use cases for DaemonSets:

1. Running a cluster storage daemon on every node.
2. Deploying a logs collection daemon on every node.
3. Monitoring nodes using a node monitoring daemon.

To create a DaemonSet, you can describe it in a YAML file. Here's an example of DaemonSet YAML for running the 


`fluentd-elasticsearch` Docker image:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
        # Tolerations allow the daemonset to run on control plane nodes
        - key: node-role.kubernetes.io/control-plane
          operator: Exists
          effect: NoSchedule
        - key: node-role.kubernetes.io/master
          operator: Exists
          effect: NoSchedule
      containers:
        - name: fluentd-elasticsearch
          image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
          resources:
            limits:
              memory: 200Mi
            requests:
              cpu: 100m
              memory: 200Mi
          volumeMounts:
            - name: varlog
              mountPath: /var/log
      volumes:
        - name: varlog
          hostPath:
            path: /var/log
```

You can create this DaemonSet using the following command:

```bash
kubectl apply -f https://k8s.io/examples/controllers/daemonset.yaml
```

Remember that a DaemonSet needs specific fields like `apiVersion`, `kind`, and `metadata`. The name of a DaemonSet object must be a valid DNS subdomain name. The `.spec.template` section defines the pod template, which should have appropriate labels and a `RestartPolicy` set to `Always`.

For more details, you can explore the official Kubernetes documentation on [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).
