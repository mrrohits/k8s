# nodeName
nodeName is a more direct form of node selection than affinity or nodeSelector. nodeName is a field in the Pod spec. If the nodeName field is not empty, the scheduler ignores the Pod and the kubelet on the named node tries to place the Pod on that node. Using nodeName overrules using nodeSelector or affinity and anti-affinity rules.

Some of the limitations of using nodeName to select nodes are:

* If the named node does not exist, the Pod will not run, and in some cases may be automatically deleted.
* If the named node does not have the resources to accommodate the Pod, the Pod will fail and its reason will indicate why, for example OutOfmemory or OutOfcpu.
* Node names in cloud environments are not always predictable or stable.

  Here is an example of a Pod spec using the nodeName field:
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.16
  nodeName: node02
```
