# 📘 Chapter-3: Projects (Namespaces) and Deployment in Kubernetes
### 🗂️ Projects / Namespaces

Namespaces (a.k.a. Projects in OpenShift) allow us to logically divide a cluster:

#### 1. Workload isolation – Separate teams/projects can run their own Pods, Volumes, and Services.
#### 2. User delegation – RBAC can be applied at the namespace level.
#### 3. Resource limits – Enforce CPU and memory quotas per namespace.

### 🔑 Roles in a Project

#### Create
#### List
#### Use
#### Delete

### Default Projects

During installation, Kubernetes already creates:

#### default

#### kube-system

#### public

### 📌 Example commands:
```
kubectl get namespaces
kubectl create ns team-1
kubectl delete ns krnetwork
```
## 🚀 Deploying Applications

To deploy an application in Kubernetes, you need:

#### 1. Namespace → e.g., default, team-1
#### 2. Image name → e.g., nginx, mysql:5.6
#### 3. Registry server → docker.io/ (public), or private registries

## Deployment Methods

#### 1. Imperative mode (CLI commands)
#### 2. Declarative mode (YAML/Manifest files)

### 📝 Example-1: Imperative Command
```
kubectl run demo --image=docker.io/nginx
kubectl get pod -o wide
kubectl exec -it demo -- bash   # Get inside container
kubectl logs demo               # Check logs
kubectl describe pod demo       # Detailed info
```

####  Deploy with namespace:
```
kubectl create ns team-1
kubectl run demo --image=docker.io/nginx -n team-1
kubectl get pods -n team-1
```
