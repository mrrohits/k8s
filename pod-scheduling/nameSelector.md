# NodeSelector

NodeSelector is a simple way to constrain Pods to nodes with specific labels. It is the basic form of node affinity and allows you to specify which node a Pod should run on based on node labels.
```

apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  nodeSelector:
    disktype: ssd   # This matches nodes that have a label `disktype=ssd`
  containers:
  - name: nginx
    image: nginx
```

In this example, the Pod will only be scheduled on nodes that have the label **disktype=ssd**.
