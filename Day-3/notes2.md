# 🔖 Labels & Selectors in Kubernetes
### 📌 What is a Label?

A **label** is an extra ***key:value pair*** assigned to Kubernetes resources.

Example:
```
zone: us
tier: backend
type: prod
```

## ✅ Uses of Labels:

### 1. Filtering → query, list, delete, or get detailed info.

### 2. Pod Scheduling → control how Pods are assigned.

### 3. Organization → categorize resources logically.

## 📌 Selector Types

Selectors allow us to filter resources based on labels.

Equality-Based (ReplicationController)

Example: tier = frontend

Set-Based (ReplicaSet)

Example: tier in (qa, prod)

🔹 Example 1: Orphan Pod (no matching controller)
kubectl get pod --show-labels
kubectl get pod --selector=colour=blue


Output:

NAME              READY   STATUS    RESTARTS   AGE   LABELS
krnetwork-6f66k   1/1     Running   0          25m   colour=blue
krnetwork-jwfzw   1/1     Running   0          25m   colour=blue

🔹 Example 2: ReplicaSet with Equality + Set-based Selectors
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

🔄 Rollout & Rollback in Kubernetes
📌 Deployment Strategies

Rolling Update (Ramped Mode) ✅

Zero downtime

Ideal for production

Gradual replacement of old Pods with new Pods

Formula:

Create: 25% new Pods  
Delete: 25% old Pods  


Example:

spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%


Recreate Strategy ⚠️

Downtime occurs

Old version terminated first, then new version created

Best for testing environments

spec:
  strategy:
    type: Recreate

📌 Deployment Benefits

Reliability → ensures Pods are always available

Scalability → scale Pods up/down easily

Rollback → revert to the previous version if issues occur
