# Stage of Compute Quota
--- 

## 1) Deployment Stage:
## 2) Namespace Stage:


## Deployment Quota:

#### 1) Requests: 
#### 2) Limits:

#### **1. Create Deployment Using `kubectl create deploy` with Dry Run**

```
kubectl create deploy test --image=docker.io/nginx --dry-run -o yaml > deploy.yml
```

#### **2. View and Edit the Generated YAML File**
  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: test
    labels:
      app: test
  spec:
    replicas: 1
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
            image: docker.io/nginx
            resources:
              requests:
                memory: 200Mi
                cpu: 200m
              limits:
                memory: 200Mi
                cpu: 200m
  ```

#### **3. Create the Deployment from YAML File**

```
kubectl create -f deploy.yml
```
#### **4. View Allocated Resources on Node**

```
kubectl describe nodes node01 | grep -iA5 allocated
```
```
  Allocated resources:
    (Total limits may be over 100 percent, i.e., overcommitted.)
    Resource           Requests     Limits
    --------           --------     ------
    cpu                325m (32%)   200m (20%)
    memory             300Mi (16%)  540Mi (28%)
  ```

#### **5. Scale Deployment**

```
kubectl scale --replicas=2 deployment test
```

#### **6. View Updated Allocated Resources on Node**

```
kubectl describe nodes node01 | grep -iA5 allocated
```
  ```
  Allocated resources:
    (Total limits may be over 100 percent, i.e., overcommitted.)
    Resource           Requests     Limits
    --------           --------     ------
    cpu                525m (52%)   400m (40%)
    memory             500Mi (26%)  740Mi (39%)
  ```

---

#### **7. Get Running Pods**

```
kubectl get pod
```
  ```
  NAME                         READY   STATUS    RESTARTS   AGE
  test-86b4f688bd-d7c5w         1/1     Running   0          13m
  test-86b4f688bd-nzlnv         1/1     Running   0          14m
  ```

* **Explanation**:

  * The deployment has 2 Pods (`test-86b4f688bd-d7c5w` and `test-86b4f688bd-nzlnv`), both running and ready.

---

#### **8. View Resource Usage with `kubectl top` (Missing Output)**

```
kubectl top nodes node01
```

### Summary of Key Commands:

1. **`kubectl create deploy`**: Creates a deployment (with `--dry-run` to preview before creating).
2. **`kubectl describe nodes`**: Displays resource allocation and utilization for nodes.
3. **`kubectl scale`**: Scales a deployment to increase or decrease the number of replicas.
4. **`kubectl get pod`**: Lists all Pods in the current namespace.
5. **`kubectl top nodes`**: Displays CPU and Memory usage statistics for nodes (requires Metrics Server).
