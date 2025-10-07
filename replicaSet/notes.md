# 🔖 Labels & Selectors in Kubernetes
### 📌 What is a Label?

A **label** is an extra ***key:value pair*** assigned to Kubernetes resources.

Example:
```
zone: us
tier: backend
type: prod
```

### ✅ Uses of Labels:

#### 1. Filtering → query, list, delete, or get detailed info.

#### 2. Pod Scheduling → control how Pods are assigned.

#### 3. Organization → categorize resources logically.

### 📌 Selector Types

Selectors allow us to filter resources based on labels.

### 1. Equality-Based (ReplicationController)

Example: tier = frontend

### 2. Set-Based (ReplicaSet)

Example: tier in (qa, prod)

#### 🔹 Example 1: Orphan Pod (no matching controller)
```
kubectl get pod --show-labels
kubectl get pod --selector=colour=blue
```

Output:
```
NAME              READY   STATUS    RESTARTS   AGE   LABELS
krnetwork-6f66k   1/1     Running   0          25m   colour=blue
krnetwork-jwfzw   1/1     Running   0          25m   colour=blue
```
#### 🔹 Example 2: ReplicaSet with Equality + Set-based Selectors
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:        # Equality-based
      tier: frontend
    matchExpressions:   # Set-based
      - key: tier
        operator: In
        values:
          - frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: docker.io/nginx
```
