# Namespace-Level Pod Scheduling in Kubernetes

Namespaces in Kubernetes are used for multi-tenancy and workload isolation. With namespace-level node selectors, you can enforce scheduling policies so that all Pods in a namespace land on specific nodes.

### 📌 1. Create a Namespace
```
kubectl create ns ygminds
```

### 📌 2. Add NodeSelector Annotation to Namespace

Edit the **namespace** to enforce scheduling rules:
```
kubectl edit ns ygminds
```

Example:
```
apiVersion: v1
kind: Namespace
metadata:
  name: ygminds
  labels:
    kubernetes.io/metadata.name: ygminds
  annotations:   # 👈 Enforces scheduling policy
    scheduler.alpha.kubernetes.io/node-selector: disk=ssd
```

#### ✅ Now, all Pods created in ygminds namespace will automatically be scheduled on nodes labeled disk=ssd.

### 📌 3. Label the Target Node
kubectl label node node01 disk=ssd

### 📌 4. Enable PodNodeSelector Admission Plugin

By default, **namespace-level** node selectors require enabling **PodNodeSelector**.

Edit **kube-apiserver** manifest:

vim /etc/kubernetes/manifests/kube-apiserver.yaml


Find:
```
--enable-admission-plugins=NodeRestriction
```

Change to include:
```
--enable-admission-plugins=NodeRestriction,PodNodeSelector
```

Save & exit → kube-apiserver will auto-restart.

### 📌 5. Deploy Application in Namespace
```
kubectl create deploy test --image=docker.io/nginx -n ygminds
```
Check placement:
```
kubectl get pods -n ygminds -o wide
```

#### ✅ Pod should run only on nodes with disk=ssd label.

##📌 Real-World Use Case

**DB Namespace:** Enforce Pods in db namespace to run only on role=db nodes.

**GPU Namespace:** Restrict ML workloads to GPU-enabled nodes.

**Frontend/Backend Isolation:** Ensure frontend Pods run on SSD nodes, backend on HDD.

Example db namespace policy:
```
metadata:
  annotations:
    scheduler.alpha.kubernetes.io/node-selector: role=db
```
