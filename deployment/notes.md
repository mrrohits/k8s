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
