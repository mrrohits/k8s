# Taint and toleration 

You add a taint to a node using kubectl taint. For example,
```
kubectl taint nodes node1 key1=value1:NoSchedule
```
places a taint on node node1. The taint has key key1, value value1, and taint effect NoSchedule. This means that no pod will be able to schedule onto node1 unless it has a matching toleration.

To remove the taint added by the command above, you can run:
```
kubectl taint nodes node1 key1=value1:NoSchedule-
```
You specify a toleration for a pod in the PodSpec. Both of the following tolerations "match" the taint created by the kubectl taint line above, and thus a pod with either toleration would be able to schedule onto node1:
```
tolerations:
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoSchedule"
```
```
tolerations:
- key: "key1"
  operator: "Exists"
  effect: "NoSchedule"
```
Here's an example of a pod that has some tolerations defined:
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "example-key"
    operator: "Exists"
    effect: "NoSchedule"
```
The default value for operator is Equal.

A toleration "matches" a taint if the keys are the same and the effects are the same, and:

* the operator is Exists (in which case no value should be specified), or
* the operator is Equal and the values should be equal.

##  Note:
There are two special cases:

If the key is empty, then the operator must be Exists, which matches all keys and values. Note that the effect still needs to be matched at the same time.

An empty effect matches all effects with key key1.
The above example used the effect of NoSchedule. Alternatively, you can use the effect of PreferNoSchedule.

The allowed values for the effect field are:

#### NoExecute
This affects pods that are already running on the node as follows:
Pods that do not tolerate the taint are evicted immediately
Pods that tolerate the taint without specifying tolerationSeconds in their toleration specification remain bound forever
Pods that tolerate the taint with a specified tolerationSeconds remain bound for the specified amount of time. After that time elapses, the node lifecycle controller evicts the Pods from the node.

#### NoSchedule
No new Pods will be scheduled on the tainted node unless they have a matching toleration. Pods currently running on the node are not evicted.
PreferNoSchedule
PreferNoSchedule is a "preference" or "soft" version of NoSchedule. The control plane will try to avoid placing a Pod that does not tolerate the taint on the node, but it is not guaranteed.

For example, imagine you taint a node like this
```
kubectl taint nodes node1 key1=value1:NoSchedule
kubectl taint nodes node1 key1=value1:NoExecute
kubectl taint nodes node1 key2=value2:NoSchedule
```

And a pod has two tolerations:
```
tolerations:
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoSchedule"
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoExecute"
```
