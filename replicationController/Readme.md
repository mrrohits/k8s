### 📄 YAML Basics

#### Filename: *.yaml or .yml

#### Format:

  #### Strings

  #### Lists (- item)

  #### Mappings (key: value)

📌 Example YAML (krnetwork.yml):
```
apiVersion: v1
kind: ReplicationController
metadata:
  name: krnetwork
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: docker.io/nginx
```

Apply the file:
```
kubectl apply -f krnetwork.yml
```
### 🔄 ReplicationController (RC)

#### Ensures reliability (Pods are always running).

#### Provides scalability (replicas can be scaled up/down).

📌 Example orphan deletion:
```
kubectl delete rc krnetwork --cascade=orphan
```

This deletes only the controller but keeps the running Pods.

### 🏷️ Labels & Selectors

**Label**  → Key-value pair attached to objects (Pods, RCs, Services, etc.)

**Selector** → Query that matches objects by labels.

📌 Example:

Pod with label:
```
metadata:
  name: nginx
  labels:
    app: web
    env: prod
```

Service selecting Pods:
```
spec:
  selector:
    app: web
    env: prod
```

