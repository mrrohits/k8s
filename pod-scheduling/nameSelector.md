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

 2) Node Label and Selector


DeploymentSide: NodeSelector:
 role=db

Deployment level ?


Example of Deployment level:

controlplane:~$ 
controlplane:~$ kubectl label node node01  disk=ssd
controlplane:~$ 
controlplane:~$ cat  deploy.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: test
  name: test
spec:
  replicas: 1
  selector:
    matchLabels:
      app: test
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: test
    spec:
      nodeSelector:  # Equality Based Selector
         disk:  ssd
      containers:
      - image: docker.io/nginx
        name: nginx
        resources: {}
status: {}
controlplane:~$ 
controlplane:~$ kubectl  create  -f  deploy.yml
controlplane:~$ 

Namespace level ?

DB namespaces ? NodeSelector: role=db ( by default disable )

Example of NameSpace level:


controlplane:~$ 
controlplane:~$ kubectl  create  ns  ygminds
controlplane:~$ kubectl edit ns  ygminds
controlplane:~$ 

apiVersion: v1
kind: Namespace
metadata:
  annotations:			# Add this annotations parameter
    scheduler.alpha.kubernetes.io/node-selector: disk=ssd  # **Edit here disk=ssd**
  creationTimestamp: "2025-05-26T21:04:56Z"
  labels:
    kubernetes.io/metadata.name: ygminds
  name: ygminds
  resourceVersion: "12865"
  uid: f3081e65-04c1-440f-84fa-3eeac58704da
spec:
  finalizers:
  - kubernetes
status:
  phase: Active

:wq!

controlplane:~$ 
controlplane:~$ kubectl  label  node  node01  disk=ssd
controlplane:~$ 

referance:- https://stackoverflow.com/questions/52487333/how-to-assign-a-namespace-to-certain-nodes

[First, you need to enable it in your kubernetes-apiserver:

Edit /etc/kubernetes/manifests/kube-apiserver.yaml:
find --enable-admission-plugins=
add PodNodeSelector parameter]
(https://stackoverflow.com/questions/52487333/how-to-assign-a-namespace-to-certain-nodes)

<img width="536" height="560" alt="image" src="https://github.com/user-attachments/assets/201ffb52-f9c7-47cd-afeb-78b023301170" />


controlplane:~$ kubectl  create  deploy  test  --image=docker.io/nginx  -n  ygminds
controlplane:~$ 
controlplane:~$ kubectl  get  pod  -n  ygminds
controlplane:~$ 
