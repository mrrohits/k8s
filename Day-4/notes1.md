# ⚡ Chapter-4: Custom Pod Scheduling in Kubernetes

Kubernetes offers multiple ways to control where Pods run in your cluster. This helps in resource optimization, high availability, and enforcing business rules.

## 📌 1. Force Pod to a Specific Node (nodeName)

### 👉 You can hard bind a Pod to a node by setting the nodeName field in the Pod spec.
```
YAML Example:

apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: worker1   # Replace with your node name
  containers:
  - name: nginx
    image: nginx
```

#### ✅ This Pod always runs on worker1, no scheduler decision is needed.

## 📌 2. Schedule Pod Using Labels & NodeSelector

Add a label to a node:
```
kubectl label nodes worker0 disktype=ssd
```

Verify node labels:
```
kubectl get nodes --show-labels
```

Create a Pod with nodeSelector:
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
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd
```

Check Pod placement:
```
kubectl get pods -o wide
```

## ✅ This Pod will only run on nodes with disktype=ssd label.

### 📌 Comparison: nodeName vs nodeSelector

Method	Use Case	Flexibility

**nodeName**	Force pod to exactly one node	❌ Rigid, not recommended in production
**nodeSelector**	Schedule on nodes with specific labels	✅ Flexible, scalable

## 📌 Best Practices

Use **nodeSelector** or **affinity/anti-affinity** rules (advanced) instead of nodeName for production.

**Label nodes based** on hardware (ssd/gpu), zone (us-east-1), or workload type (frontend/backend).

Keep labels consistent to avoid scheduling issues.
