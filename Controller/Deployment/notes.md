# 🔄 Rollout & Rollback in Kubernetes
## 📌 Deployment Strategies

#### 1.Rolling Update (Ramped Mode) ✅

  * Zero downtime

  * Ideal for production

  * Gradual replacement of old Pods with new Pods

Formula:
```
Create: 25% new Pods  
Delete: 25% old Pods  
```

Example:
```
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
```
## Creating a deployment 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test
  labels:
    app: test
spec:
  replicas: 3
  selector:
    matchLabels:
      app: test
  template:
    metadata:
      labels:
        app: test
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
```

Let's update the nginx Pods to use the nginx:1.16.1 image instead of the nginx:1.14.2 image.
```
kubectl set image deployment.v1.apps/nginx-deployment nginx=nginx:1.16.1
```
or use the following command:
```
kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1
```
The output is similar to:
```
deployment.apps/nginx-deployment image updated
```

Alternatively, you can edit the Deployment and change .spec.template.spec.containers[0].image from nginx:1.14.2 to nginx:1.16.1:
```
kubectl edit deployment/nginx-deployment
```
The output is similar to:
```
deployment.apps/nginx-deployment edited
```
To see the rollout status, run:
```
kubectl rollout status deployment/nginx-deploymen
```
### Recreate Strategy ⚠️

  * Downtime occurs

  * Old version terminated first, then new version created

  * Best for testing environments
```
spec:
  strategy:
    type: Recreate
```
### 📌 Deployment Benefits

Reliability → ensures Pods are always available

Scalability → scale Pods up/down easily

Rollback → revert to the previous version if issues occur
