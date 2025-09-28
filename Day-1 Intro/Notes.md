# 📘 Lecture 1: Kubernetes Introduction

This document contains installation steps, architecture details, and advantages of Kubernetes for easy reference.

### ⚙️ Kubernetes Installation
### 🖥️ Required Hardware

#### OS: Ubuntu 22.04
#### 3 GiB RAM
#### 40 GiB Storage
#### 2 vCPU

## 🌐 Network Configuration
```
root@k8s-master:~# cat /etc/netplan/99_config.yaml
network:
    version: 2
    renderer: networkd
    ethernets:
        ens34:
            addresses:
            - 192.168.22.1/24

root@k8s-master:~# ip a s ens34
3: ens34: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    inet 192.168.22.1/24 brd 192.168.22.255 scope global ens34
```

#### 🔧 System Preparation
```
swapoff -a
modprobe br_netfilter
sysctl -w net.ipv4.ip_forward=1
```
#### 📦 Install Kubernetes & CRI-O
```
KUBERNETES_VERSION=v1.32
CRIO_VERSION=v1.32

apt-get update -y && apt-get upgrade -y
apt-get install -y software-properties-common curl
```
Add Kubernetes repo:

```
curl -fsSL https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/deb/Release.key | \
    gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/deb/ /" | \
    tee /etc/apt/sources.list.d/kubernetes.list
```
Add CRI-O repo:
```
curl -fsSL https://download.opensuse.org/repositories/isv:/cri-o:/stable:/$CRIO_VERSION/deb/Release.key | \
    gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] \
https://download.opensuse.org/repositories/isv:/cri-o:/stable:/$CRIO_VERSION/deb/ /" | \
    tee /etc/apt/sources.list.d/cri-o.list
```

Install CRI-O:
```
apt-get update
apt-get install -y cri-o
systemctl start crio
systemctl enable crio
```

Install Kubernetes components:
```
apt-get install -y kubelet kubeadm kubectl
systemctl start kubelet
systemctl enable kubelet
```
Initialize cluster:
```
kubeadm init --apiserver-advertise-address=1<your_ip> --ignore-preflight-errors=all
```

Configure kubeconfig:

mkdir -p $HOME/.kube
cp /etc/kubernetes/admin.conf $HOME/.kube/config

### 🎯 Why Kubernetes?

#### Kubernetes is an open-source container orchestration tool that provides:
```
1. Centralized Management – Manage all container hosts from a single master node.

2. Cluster Topology & Failover – Containers can migrate between nodes.

3. Reliability – Automatic container recovery reduces downtime.

4. Scalability – Scale apps up/down horizontally or vertically.

5. User Management – Multiple users can manage clusters.

6. RBAC (Role-Based Access Control) – Assign limited permissions.

7. Project/Namespace Management – Isolate resources per team/project.
```
## 🏗️ Kubernetes Architecture Diagram
```
                   +-----------------------------+
                   |     Control Plane (Master)  |
                   |-----------------------------|
                   |  - kube-apiserver           |
                   |  - etcd (key-value store)   |
                   |  - kube-scheduler           |
                   |  - kube-controller-manager  |
                   +---------------+-------------+
                                   |
                                   | API / Instructions
                                   v
        ---------------------------------------------------------
        |                         Worker Nodes                  |
        |-------------------------------------------------------|
        | Node-1                      Node-2                   |
        | ---------------------       ---------------------     |
        | - kubelet           |       | - kubelet           |   |
        | - kube-proxy        |       | - kube-proxy        |   |
        | - CRI-O (runtime)   |       | - CRI-O (runtime)   |   |
        |   +-----------------|       |   +-----------------
```
# 🏗️ Kubernetes Architecture
## 🔹 Control Plane (Master Node)

kube-apiserver → Authentication & API validation

etcd → Key-value store for cluster state

kube-scheduler → Decides best node for pods

Default scheduling

Custom scheduling (affinity, taints & tolerations)

kube-controller → Ensures desired state (e.g., replicas=4 pods)

## 🔹 Worker Nodes (Data Plane)

CRI-O (Container Runtime) → Runs containers

kubelet → Agent on each node, communicates with API server

kube-proxy → Handles pod networking

# ☁️ Kubernetes Deployment Models

User-Managed → IT team handles infra, OS, deployment, monitoring, storage, upgrades.

Cloud-Managed (PaaS Model) → Control plane handled by cloud providers:

AWS → EKS

GCP → GKE

Azure → AKS

Enterprise Kubernetes (Commercial offerings):

Red Hat OpenShift

SUSE Rancher

VMware Tanzu

# ✅ Summary

Installed Kubernetes with CRI-O runtime

Understood Kubernetes architecture (Control Plane & Worker Nodes)

Learned advantages: management, reliability, scalability, RBAC, HA

Deployment options: Self-managed, Cloud-managed, Enterprise
