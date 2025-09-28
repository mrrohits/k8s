Lecture-1: K8s Introduction
============================================================

Installation k8s
========================================================


#### Required Hardware ####

OSI ubuntu 22.04

	3 Gi Ram
	40 Gi Storage
	2 CPU
_____________________________________

root@k8s-master:~# 
root@k8s-master:~# cat    /etc/netplan/99_config.yaml 
network:
    version: 2
    renderer: networkd
    ethernets:
        ens34:
            addresses:
            - 192.168.22.1/24
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# ip a s ens34
3: ens34: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:52:a8:80 brd ff:ff:ff:ff:ff:ff
    altname enp2s2
    inet 192.168.22.1/24 brd 192.168.22.255 scope global ens34
       valid_lft forever preferred_lft forever
    inet6 fe80::c484:f99:f001:e4df/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
root@k8s-master:~# 
root@k8s-master:~# 


swapoff -a
modprobe br_netfilter
sysctl -w net.ipv4.ip_forward=1

KUBERNETES_VERSION=v1.32
CRIO_VERSION=v1.32

apt-get update  -y  &&  apt-get upgrade -y
apt-get install -y software-properties-common curl

curl -fsSL https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/deb/Release.key |
    gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/deb/ /" |
    tee /etc/apt/sources.list.d/kubernetes.list


curl -fsSL https://download.opensuse.org/repositories/isv:/cri-o:/stable:/$CRIO_VERSION/deb/Release.key |
    gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://download.opensuse.org/repositories/isv:/cri-o:/stable:/$CRIO_VERSION/deb/ /" |
    tee /etc/apt/sources.list.d/cri-o.list


apt-get update
apt-get install -y cri-o

systemctl  start  crio
systemctl enable crio

apt-get install -y  kubelet kubeadm kubectl

systemctl  start  kubelet
systemctl enable kubelet

kubeadm   init  --apiserver-advertise-address=192.168.22.1  --ignore-preflight-errors=all

mkdir  .kube

cp  /etc/kubernetes/admin.conf   .kube/






1. Management
2. Reliability
3. Scalability
4. UserManagment
5. Role Access Control
6. High-Availability (Fail-over)

Kubernetes: it is an opensource container orchestration management tools 

Advantage of Kubernetes/orchestration tools.

1. Centralized Management :

	 1. now we can managed all the Container Host/Node from Single Management Servers.
	 2. Administrator now they provide container instructions from orchestration node.
 	 3. no required to login directly on each Docker Host.

2. Cluster Topology(Failover Domain):

 	1. now container can migrate one DockerHost to Another DockerHost.

3. Reliability:

 	1. if any container goes down , it recover automatically in your absence,
	 2. it optimize application down time.

4. Scalability:

 	1. anytime we can add container for the running application on de-demand .
 	2. manually/automatically ( k8s it support both)
 	3. Horizontal/vertical form ( it support both method)

5. userManagment:

	 1. we can logged with different-2 user account .

6. Role based access control:

 	now we provide limited permission to administrator
	1) Super administrator
	2) Limited permission administrator .

7. Project Management :

 using we can isolate resources between the team .







K8s Arch:

Control Plane/Master Node: 


 1. this is seprate management node .
 2. administrators are giving instruction Master Node.


DataPlane/Worker Node:

 There we are running our application as container. Instruction given by master Node.

Pod=Containers

 DatabaseNode
 Application Node
 DNS node
 Monitoring Node
 Ingress control Node/balancers.


K8s Service Introductions:

Control Plane:

 1. kub-api :
 	1. authentication
	 2. command validation(Authorization)

 2. kube-etcd(data Store) :
 	. it store all api/cluster information in the key: value form

 3. kube-scheduler: it provide best fit node information to api.
 	1) Default
 	2) Custom scheduling:
 		1) affinity - ant affinity
 		2) taint and toleration

 4. kube-controller:

 	desired state: --replicas=4 pods
 	running/current state
Command---kube-api---etcd---scheduler---controller 

WorkerNodes:

1. container run time: cri-o
2. kubelet: it the agent of Kub-api . Which receive the instruction further pass to runtime(Cri-oi)
3. kube-proxy: this is network related components which we will pod networking chapter 


K8s:

1) User Managed form: all ops managed by your IT team. (Infra + OS + Installation + deployment + network + monitoring + storage + Upgrade )

2) Cloud Managed form:

	PAAS Model: Control Plane + Installation managed by cloud-team 

	Amazon: EKS
	Google: GKE
	Azure: AKS

	Kubernetes Enterprise: 

1) Redhat: openshift: this is also orchestration based on kubernetes .
2) Suse: Ranchor
3) Vmware: Tanzu

	Lab Setup and Installation ?

============================================================
Lecture-2: Lab Setup and Installation
============================================================

Control Plane :
 	Memory: 3Gi
 	Cpu: 2
 	Hard Disk: 10Gi

Worker1:
 	Memory: 2Gi
 	cpu: 2
 	hard disk: 10Gi

Worker2

Ubuntu : 20 +

Local laptop:

Installation Method: 

1) Container Mode:

	 Master node preparation

Systemd Mode

Step-1: Install first any container runtime (cri-o)

Step-2: Install the kubelet 

API Images:
Etcd Image:
Controller:
Schedulers:

Pull
RUN
Api ---etcd
Certificate generate
Config file -- API Service


Step-3: they develop utility(Script ) for initializing control plane.
 	1) Kubeadm : Stable : by default provide by kubernetes community
 	2) Ansible
 	3) KOPS
 	4) KUbSpray


Step-1: Cri-o Installtion

curl -fsSL https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/Release.key |
 gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://pkgs.k8s.io/addons:/crio:/prerelease:/main/deb/ /" |
 tee /etc/apt/sources.list.d/cri-o.list



Step-2:Install kubelet and Kubeadm 

sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee
/etc/apt/sources.list.d/kubernetes.list

apt-get update

apt-get install -y kubelet kubeadm


Step-3: Start API/Etcd/scheduler/controller pod .


Kubelet: it agent which runs on each node including master
Kubeadm: installation utility : it has inilized master node components .
Kubectl: this is command like docker . By we provide any instruction to api .


Master node: 

How to logged in API Server:

     APiServer:PortNumber
	 username:
	 Password:
root@k8s-master:~#
root@k8s-master:~# kubectl get nodes
NAME STATUS ROLES AGE VERSION
k8s-master Ready control-plane 19m v1.30.0
root@k8s-master:~# history
 1 clear
 2 ip a s
 3 vim /etc/netplan/99_config.yaml
 4 ip a s
 5 vim /etc/netplan/99_config.yaml
 6 netplan apply
 7 clear
 8 ip a s
 9 ping 192.168.22.1
 10 curl -fsSL https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg
 11 echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb//" | tee /etc/apt/sources.list.d/cri-o.list
 12 apt-get update
 13 clear
 14 apt install cri-o -y
 15 clear
 16 systemctl start cri-o
 17 systemctl enable cri-o
 18 systemctl status cri-o
 19 curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
 20 echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb//' | sudo tee /etc/apt/sources.list.d/kubernetes.list
 21 apt-get update
 22 clear
 23 apt install kubelet kubeadm
 24 systemctl start kubelet
 25 systemctl enable kubelet
 26 kubeadm init --help
 27 kubeadm init --apiserver-advertise-address=192.168.22.1
 28 kubeadm init --apiserver-advertise-address=192.168.22.1 --ignore-preflight-errors=all
 29 docker ps
 30 clear
 31 kubectl
 32 apt install kubectl -y
 33 kubectl get pod
 34 kubectl get nodes
 35 kubectl config get-contexts
 36 kubectl get nodes --kubeconfig=/etc/kubernetes/admin.conf
 37 cp /etc/kubernetes/admin.conf .kube/config
 38 kubectl get nodes
 39 kubectl config get-contexts
 40 kubectl get pod -A
 41 systemctl is-active kubelet
 42 systemctl is-active cri-o
 43 kubectl get componentstatus
 44 clear
 45 kubectl get nodes
root@k8s-master:~#
root@k8s-master:~# kubeadm token list
TOKEN            TTL    EXPIRES    USAGES          DESCRIPTION                            EXTRA GROUPS
9zy54n.pigh1nu9zxzcj7hh 23h 2024-05-13T10:44:09Z authentication,signing The default bootstrap token generated by 'kubeadm init'. system:bootstrappers:kubeadm:default-nodetoken
root@k8s-master:~#
root@k8s-master:~#
root@k8s-master:~# kubeadm token create
fw9087.4nrje80fjq71x9cs
root@k8s-master:~#
root@k8s-master:~# kubeadm token delete 9zy54n.pigh1nu9zxzcj7hh
bootstrap token "9zy54n" deleted
root@k8s-master:~#
root@k8s-master:~# kubeadm token delete fw9087.4nrje80fjq71x9cs
bootstrap token "fw9087" deleted
root@k8s-master:~#
root@k8s-master:~# kubeadm token create --print-join-command
kubeadm join 192.168.22.1:6443 --token y8n94k.lba5h48vpt2tobpl --discovery-token-ca-cert-hash sha256:cff16acd78e3495203ccd183d71b3fd7a40fe10bd98dd63241321639477d6509
root@k8s-master:~#


Worker Node:
step-1: Cri-o
Step-2: kubelet
Step-3: cluster Join
APIServer
Token
CA Certificate 


root@worker-1:~#
root@worker-1:~# systemctl start cri-o
root@worker-1:~# systemctl start kubelet
root@worker-1:~#
root@worker-1:~# systemctl enable cri-o
Failed to enable unit: Refusing to operate on alias name or linked unit file: cri-o.service
root@worker-1:~#
root@worker-1:~# systemctl enable kubelet
root@worker-1:~#
root@worker-1:~# kubeadm join 192.168.22.1:6443 --token y8n94k.lba5h48vpt2tobpl --discovery-token-ca-cert-hash
sha256:cff16acd78e3495203ccd183d71b3fd7a40fe10bd98dd63241321639477d6509
Command 'kubeadm' not found, but can be installed with:
snap install kubeadm
root@worker-1:~#
root@worker-1:~#
root@worker-1:~# apt install kubeadm -y
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
 cri-tools
The following NEW packages will be installed:
 cri-tools kubeadm
0 upgraded, 2 newly installed, 0 to remove and 65 not upgraded.
Need to get 31.7 MB of archives.
After this operation, 109 MB of additional disk space will be used.
Get:1 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.30/deb cri-tools 1.30.0-1.1 [21.3 MB]
Get:2 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.30/deb kubeadm 1.30.0-1.1 [10.4 MB]
Fetched 31.7 MB in 4s (8,412 kB/s)
Selecting previously unselected package cri-tools.
(Reading database ... 74687 files and directories currently installed.)
Preparing to unpack .../cri-tools_1.30.0-1.1_amd64.deb ...
Unpacking cri-tools (1.30.0-1.1) ...
Selecting previously unselected package kubeadm.
Preparing to unpack .../kubeadm_1.30.0-1.1_amd64.deb ...
Unpacking kubeadm (1.30.0-1.1) ...
Setting up cri-tools (1.30.0-1.1) ...
Setting up kubeadm (1.30.0-1.1) ...
Scanning processes...
Scanning linux images...
Running kernel seems to be up-to-date.
No services need to be restarted.
No containers need to be restarted.
No user sessions are running outdated binaries.
No VM guests are running outdated hypervisor (qemu) binaries on this host.
root@worker-1:~#
root@worker-1:~# kubeadm join 192.168.22.1:6443 --token y8n94k.lba5h48vpt2tobpl --discovery-token-ca-cert-hash
sha256:cff16acd78e3495203ccd183d71b3fd7a40fe10bd98dd63241321639477d6509
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
 [ERROR FileContent--proc-sys-net-ipv4-ip_forward]: /proc/sys/net/ipv4/ip_forward contents are not set to 1
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher
root@worker-1:~#
root@worker-1:~# kubeadm join 192.168.22.1:6443 --token y8n94k.lba5h48vpt2tobpl --discovery-token-ca-cert-hash
sha256:cff16acd78e3495203ccd183d71b3fd7a40fe10bd98dd63241321639477d6509 --ignore-preflight-errors=all
[preflight] Running pre-flight checks
 [WARNING FileContent--proc-sys-net-ipv4-ip_forward]: /proc/sys/net/ipv4/ip_forward contents are not set to 1
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Starting the kubelet
[kubelet-check] Waiting for a healthy kubelet. This can take up to 4m0s
[kubelet-check] The kubelet is healthy after 506.31115ms
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap
This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.
Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
root@worker-1:~# ls /var/lib/kubelet/pki/
kubelet-client-2024-05-12-11-35-46.pem kubelet-client-current.pem kubelet.crt kubelet.key
root@worker-1:~#
root@worker-1:~#
root@worker-1:~#
root@worker-1:~#
root@worker-1:~# history
1 clear
 2 ip a s
 3 vi /etc/netplan/99_config.yaml
 4 clear
 5 netplan apply
 6 clear
 7 ip a s
 8 ping 192.168.22.1
 9 clear
 10 curl -fsSL https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg
 11 echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb//" | tee /etc/apt/sources.list.d/cri-o.list
 12 apt update -y ; apt install cri-o -y
 13 apt-get install -y apt-transport-https ca-certificates curl gpg
 14 curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
 15 echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb//' | sudo tee /etc/apt/sources.list.d/kubernetes.list
 16 apt update -y ; apt install kubelet -y
 17 clear
 18 systemctl start cri-o
 19 systemctl start kubelet
 20 systemctl enable cri-o
 21 systemctl enable kubelet
 22 kubeadm join 192.168.22.1:6443 --token y8n94k.lba5h48vpt2tobpl --discovery-token-ca-cert-hash sha256:cff16acd78e3495203ccd183d71b3fd7a40fe10bd98dd63241321639477d6509
 23 apt install kubeadm -y
 24 kubeadm join 192.168.22.1:6443 --token y8n94k.lba5h48vpt2tobpl --discovery-token-ca-cert-hash sha256:cff16acd78e3495203ccd183d71b3fd7a40fe10bd98dd63241321639477d6509
 25 kubeadm join 192.168.22.1:6443 --token y8n94k.lba5h48vpt2tobpl --discovery-token-ca-cert-hash sha256:cff16acd78e3495203ccd183d71b3fd7a40fe10bd98dd63241321639477d6509 --
ignore-preflight-errors=all
 26 ls /var/lib/kubelet/pki/
 27 history
root@worker-1:~#

Network Configuration ?

Kubernetes does not use default Docker bridge Network ?

L3 network: calico: it is the software l3 network .

Now we are install kubernetes cluster using vagrant scripts in local . ? 

Step-1: GIT Client
Step-2: VirtualBox:
Step-3: Vagrant

Login: vagrant
Password: vagrant:
Sudo full :


1) Systemd Mode: not recommended : 

Cloud based:


============================================================
Chapter-3: Project and deployment
============================================================


What are the projects/namespaces : ?

1) Based on the project we can isolate teams workload (Pods/volumes/) .
2) Based on the project we can delegate the users .
3) We can limit cpu/memory usages in the cluster .


Roles:
 1) Create
 2) List
 3) Use
 4) Delete


Project :

 1) default project: during the installation already has created (default,kube-system,public)


controlplane:~$ history
    1  kubectl get pod -n kube-system
    2  kubectl   get   namespaces 
    3  kubectl   get   ns
    4  kubectl get pod -n kube-system -o wide
    5  kubectl create ns krnetwork
    6  kubectl create ns redhat
    7  kubectl   get   ns
    8  kubectl get pod -n krnetwork
    9  kubectl delete ns krnetwork
   10  history
controlplane:~$   


How to Deploy application pod In the cluster :

1) Project Name/namespace ?
2) Image name ?
3) Registry Servers ?
 docker.io/ : public registry 


What are the ways ? 

1) Imperative mode: command line
2) Declarative Mode: manifests file/yaml file based 


Example-1: how to create first pod using command line ?


Kubectl options 

controlplane:~$ history
    1  kubectl run demo --image=docker.io/nginx
    2  kubectl   get  pod
    3  kubectl   get  pod -owide
    4  ping  192.168.1.9
    5  kubectl  exec  -it  demo  -- bash
    6  kubectl get pod -o wide
    7  kubectl  describe  pod  demo   #Check out detailed informaion
    8  kubectl logs  demo  #Check out log information
    9  kubectl run db --env MYSQL_ROOT_PASSWORD=redhat --image=mysql:5.6 
   10  kubectl  describe  pod  demo
   11  kubectl  get  events # Get API log details
   12  kubectl run demo --image=docker.io/nginx -n team-1
   13  kubectl create ns team-1
   14  kubectl run demo --image=docker.io/nginx -n team-1
   15  kubectl  get  events
   16  kubectl get ns team-1
   17 kubectl get pod -n default
   18 kubectl get pod -n team-1
controlplane:~$ 



Key: value (yaml formate)

1) FileName: *.yaml or yaml
2) Yaml
 	1) String
 	2) list
 	3) Mapping


Vim resources.yml


apiVersion:
Kind: PersistentVolumes
Metadata:
Spec:
_________________________________________

Resources scopes:

1) Cluster .
2) Namespaces .


1. Orphans 

2. Controller pod
 	1. reliability
	2. scalability 

ReplicationController: RC

Deploying application through RC controllers. 


root@controlplane:~#
root@controlplane:~#
root@controlplane:~# kubectl delete rc krnetwork --cascade='orphan'   # You want to remove the controller but keep the running application temporarily.
replicationcontroller "krnetwork" deleted
root@controlplane:~#
root@controlplane:~# kubectl get rc
No resources found in default namespace.
root@controlplane:~#
root@controlplane:~# kubectl get pod
NAME READY STATUS RESTARTS AGE
alpine 1/1 Running 0 42m
krnetwork-4np7b 1/1 Running 0 3m41s
krnetwork-6xts6 1/1 Running 0 3m41s
mysql 1/1 Running 0 89m
ram 1/1 Running 0 73m
test-7955cf7657-vvmwr 1/1 Running 0 25m
root@controlplane:~#
root@controlplane:~# vim krnetwork.yml
root@controlplane:~#
root@controlplane:~# kubectl apply -f krnetwork.yml
replicationcontroller/krnetwork created
root@controlplane:~#
root@controlplane:~#
root@controlplane:~# cat krnetwork.yml
apiVersion: v1
kind: ReplicationController
metadata:
 annotations:
 kubectl.kubernetes.io/last-applied-configuration: |
{"apiVersion":"v1","kind":"ReplicationController","metadata":{"annotations":{},"name":"krnetwork","namespace":"default"},"spec":{"replicas":7,"selector":{"ap
p":"nginx"},"template":{"metadata":{"labels":{"app":"nginx"},"name":"nginx"},"spec":{"containers":[{"image":"docker.io/nginx","name":"nginx"}]}}}}
 creationTimestamp: "2024-05-18T11:43:13Z"
 generation: 6
 labels:
 app: nginx
 name: krnetwork
 namespace: default
 resourceVersion: "162341"
 uid: 0d212d11-bca3-4a19-8af1-667de3ac990d
spec:
 replicas: 8
 selector:
 app: nginx
 template:
 metadata:
 creationTimestamp: null
 labels:
 app: nginx
 name: nginx
name: nginx
 spec:
 containers:
 - image: docker.io/nginx
 imagePullPolicy: Always
 name: nginx
 resources: {}
 terminationMessagePath: /dev/termination-log
 terminationMessagePolicy: File
 dnsPolicy: ClusterFirst
 restartPolicy: Always
 schedulerName: default-scheduler
 securityContext: {}
 terminationGracePeriodSeconds: 30
status:
 availableReplicas: 2
 fullyLabeledReplicas: 2
 observedGeneration: 6
 readyReplicas: 2
 replicas: 2
root@controlplane:~#


Label And Selector ? 


=======================================================================
Label and Selector:
=======================================================================

Label: this is extra identical key: value the api resources.

 zone: us
 tier: backend
 type: prod

1) Filter ( query list/delete/information)

2) Pod scheduling

Command line
Manifests

Example-1 orphan pod ? 

Example-2: RC label ? 

Selector:

 1) Set Based Mode: RS

 2) Equality mode: RC

RS: Replicas Set:

 1) Reliability
 2) pod add / pod remove on-demand 

RS:

 Set Based Selector
 Equality based

controlplane:~$ kubectl  get  pod  --show-labels
NAME              READY   STATUS    RESTARTS   AGE   LABELS
krnetwork-6f66k   1/1     Running   0          25m   colour=blue
krnetwork-jwfzw   1/1     Running   0          25m   colour=blue

controlplane:~$ kubectl  get  pod  --selector=colour=blue
NAME              READY   STATUS    RESTARTS   AGE
krnetwork-6f66k   1/1     Running   0          27m
krnetwork-jwfzw   1/1     Running   0          27ms

apiVersion: apps/v1
kind: ReplicaSet
metadata:
 name: frontend
spec:
 # modify replicas according to your case
 replicas: 3
 selector:
 matchLabels: # Equality based
 tier: frontend
 matchExperssion: # Set based
 - { 'tier' in (qa, prod )}
 - { color in blue }
 template:
 metadata:
 labels:
 tier: frontend
 spec:
 containers:
 - name: nginx
 image: docker.io/nginx





controlplane:~$ cat  rs.yml 
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
    matchExpressions:  # Set based
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

controlplane:~$ 

controlplane:~$ cat   rs.yml 
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: krnetwork
  labels:
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchExpressions:
        - { key: tier, operator: In, values: [frontend]}
        - { key: tier, operator: NotIn, values: [backend]}
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: docker.io/nginx

=======================================================================
Rolling out and Rollback
=======================================================================


Rollout strategy :

 1. Ramped Mode/Rollingout:

     advantage: zero downtime
     Live upgrade: prod

--replicas=10
Image: nginx:1.16
Image: nginx:1.17
 formula:
 create: 25%
 delete: 25%


Deployment:

   1) reliability
   2) Scalability

1) Command
2) Manifests


2. Recreate:

    cons: downtime
    first it old version and it create new version:
    test:

==========================================================================

Custom Pod Scheduling :
__________________________

 1) Node Named based

 note: not required kube-schedule in this case if
 u are putting node name directly in deploy file.

Example: nodeName: 

1. NodeName Based Pod scheduling ?

apiVersion: apps/v1
kind: Deployment
metadata:
 creationTimestamp: null
 labels:
 app: test
 name: test
spec:
 replicas: 1
 selector:
 matchLabels:
 app: test
 strategy: {}
 template:
 metadata:
 creationTimestamp: null
 labels:
 app: test
 spec:
 nodeName: node02	
 containers:
 - image: nginx
 name: nginx
 resources: {}


 2) Node Label and Selector


DeploymentSide: NodeSelector:
 role=db

Deployment level ?


Example of Deployment level:

controlplane:~$ 
controlplane:~$ kubectl label node node01  disk=ssd
controlplane:~$ 
controlplane:~$ cat  deploy.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: test
  name: test
spec:
  replicas: 1
  selector:
    matchLabels:
      app: test
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: test
    spec:
      nodeSelector:  # Equality Based Selector
         disk:  ssd
      containers:
      - image: docker.io/nginx
        name: nginx
        resources: {}
status: {}
controlplane:~$ 
controlplane:~$ kubectl  create  -f  deploy.yml
controlplane:~$ 

Namespace level ?

DB namespaces ? NodeSelector: role=db ( by default disable )

Example of NameSpace level:


controlplane:~$ 
controlplane:~$ kubectl  create  ns  ygminds
controlplane:~$ kubectl edit ns  ygminds
controlplane:~$ 

apiVersion: v1
kind: Namespace
metadata:
  annotations:			# Add this annotations parameter
    scheduler.alpha.kubernetes.io/node-selector: disk=ssd
  creationTimestamp: "2025-05-26T21:04:56Z"
  labels:
    kubernetes.io/metadata.name: ygminds
  name: ygminds
  resourceVersion: "12865"
  uid: f3081e65-04c1-440f-84fa-3eeac58704da
spec:
  finalizers:
  - kubernetes
status:
  phase: Active

:wq!

controlplane:~$ 
controlplane:~$ kubectl  label  node  node01  disk=ssd
controlplane:~$ 

First, you need to enable it in your kubernetes-apiserver:

Edit /etc/kubernetes/manifests/kube-apiserver.yaml:
find --enable-admission-plugins=
add PodNodeSelector parameter

controlplane:~$ kubectl  create  deploy  test  --image=docker.io/nginx  -n  ygminds
controlplane:~$ 
controlplane:~$ kubectl  get  pod  -n  ygminds
controlplane:~$ 

 3) Affinity and anti-affinity based


Node Affinity and anti-affinity rules ?

Zone=power1a
 power1b

MatchExpressions: (operator)
Affinity:
In
Exists

Anti-affinity
NotIn
DoesNotExists

Affinity:
 1) Hard:
 2) Soft (prefered)



#### node affinity rule in kubernetes ####

apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: krnetwork
  name: krnetwork
spec:
  replicas: 1
  selector:
    matchLabels:
      app: krnetwork
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: krnetwork
    spec:
      affinity:
        nodeAffinity:  # this is the example of nodeaffinity
          requiredDuringSchedulingIgnoredDuringExecution:  # this is the example of hard affinity
            nodeSelectorTerms:
              - matchExpressions:
                - key: disk
                  operator: NotIn
                  values:
                    - ssd
      containers:
      - image: docker.io/nginx
        name: nginx
        resources: {}
status: {}



#### Soft Affinity ####


apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: krnetwork
  name: krnetwork
spec:
  replicas: 1
  selector:
    matchLabels:
      app: krnetwork
  template:
    metadata:
      labels:
        app: krnetwork
    spec:
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - weight: 100
              preference:
                matchExpressions:
                  - key: zone
                    operator: In
                    values:
                      - us-east1
      containers:
        - image: docker.io/nginx
          name: nginx
          resources: {}


apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: krnetwork
  name: krnetwork
spec:
  replicas: 1
  selector:
    matchLabels:
      app: krnetwork
  template:
    metadata:
      labels:
        app: krnetwork
    spec:
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - weight: 100
              preference:
                matchExpressions:
                  - key: zone
                    operator: Exists   #when we use Exists or NotExists no need to defined values
                    
      containers:
        - image: docker.io/nginx
          name: nginx
          resources: {}

####  Pod Affinity Based ####



 4) Taint and toleration based 


Taint and tolerations ?

Tanancy:
 1) Shared
 2) dedicate

Taint rule: Node side

Tolerations side: deployment side

controlplane:~$ cat  deploy.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      nodeSelector:
        tier: frontend
      tolerations:
        - key:  area
          operator: Exists
          effect: NoSchedule
      containers:
      - image: docker.io/nginx
        name: nginx
controlplane:~$ 
controlplane:~$ history
    1  exit
    2  halt
    3  FILE=/ks/wait-background.sh; while ! test -f ${FILE}; do clear; sleep 0.1; done; bash ${FILE}
    4  kubectl   create  deploy  web  --image=docker.io/nginx  --dry-run  -o  yaml  >  deploy.yaml
    5  vim deploy.yaml 
    6  kubectl   describe   nodes  controlplane   |  grep -i   taint
    7  vim  deploy.yaml 
    8  kubectl   create  -f  deploy.yaml 
    9  vim  deploy.yaml 
   10  kubectl   create  -f  deploy.yaml 
   11  vim  deploy.yaml 
   12  kubectl  describe   nodes controlplane  | grep -i  taint
   13  vim  deploy.yaml 
   14  kubectl   create  -f  deploy.yaml 
   15  vim  deploy.yaml 
   16  kubectl   create  -f  deploy.yaml 
   17  kubectl   get  pod 
   18  kubectl  scale  deployment  --replicas=5  web 
   19  kubectl   get  pod
   20  kubectl   get  pod -owide
   21  kubectl   delete  -f  deploy.yaml 
   22  kubectl   get  pod -owide
   23  vim  deploy.yaml 
   24  kubectl  label   nodes controlplane   tier=frontend
   25  kubectl   create   -f  deploy.yaml 
   26  kubectl  get pod
   27  kubectl  get pod -owide
   28  kubectl  scale  deployment  --replicas=5  web 
   29  kubectl  get pod -owide
   30  kubectl  scale  deployment  --replicas=10  web 
   31  kubectl  get pod -owide
   32  kubectl  label  nodes  node01  tier=frontend
   33  kubectl  get pod -owide
   34  kubectl  scale  deployment  --replicas=1  web 
   35  kubectl  scale  deployment  --replicas=10  web 
   36  kubectl  get pod -owide
   37  kubectl   describe   nodes  controlplane  |  grep -i  taint
   38  kubectl  taint   node  node01  area:NoSchedule
   39  kubectl  taint   node  controlplane  area:NoSchedule
   40  vim deploy.yaml 
   41  kubectl   edit  deploy  web 
   42  kubectl  get  pod -owide
   43  kubectl  taint   node  controlplane  area:NoSchedule-
   44  kubectl   scale  deployment web --replicas=1
   45  kubectl   scale  deployment web --replicas=10
   46  kubectl  get  pod -owide
   47  kubectl  edit   nodes node01 
   48  kubectl   scale  deployment web --replicas=1
   49  kubectl   scale  deployment web --replicas=10
   50  kubectl  get  pod -owide
   51  kubectl   edit   nodes  controlplane 
   52  kubectl  describe  nodes  controlplane | grep -i  taint
   53  kubectl   edit  deploy  web 
   54  kubectl  get  pod -owide
   55  kubectl  scale  deployment web  --replicas=1
   56  kubectl  get  pod -owide
   57  watch kubectl  get  pod -owide
   58  kubectl  scale  deployment web  --replicas=10
   59  watch kubectl  get  pod -owide
   60  cat  deploy.yaml 
   61  history
controlplane:~$ 

==========================================================================
Compute and Resource Quota
==========================================================================

Compute: CPU/Memory Usages limit

Memory: 100Mi/Gi
CPU: 1 = 1000m

200m
0.2
0.5


Stage of Compute Quota

1) Deployment Stage:


2) Namespace Stage:


Deployment Quota:
	1) Requests: 
	2) Limits:

controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  create  deploy  test  --image=docker.io/nginx  --dry-run  -o  yaml  >  deploy.yml 
W0528 00:42:53.597426   88544 helpers.go:702] --dry-run is deprecated and can be replaced with --dry-run=client.
controlplane:~$ 
controlplane:~$ vim  deploy.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: test
  name: test
spec:
  replicas: 1
  selector:
    matchLabels:
      app: test
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: test
    spec:
      containers:
      - image: docker.io/nginx
        name: nginx
        resources: 
          requests:
            memory: 200Mi
            cpu: 200m
          limits:
            memory: 200Mi
            cpu: 200m

:wq!
 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  create  -f  deploy.yml 
deployment.apps/test created
controlplane:~$ 
controlplane:~$ kubectl  describe  nodes  node01 | grep -iA5  allocated
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests     Limits
  --------           --------     ------
  cpu                325m (32%)   200m (20%)
  memory             300Mi (16%)  540Mi (28%)
controlplane:~$ 
controlplane:~$ kubectl  scale  --replicas=2  deployment  test 
deployment.apps/test scaled
controlplane:~$ 
controlplane:~$ kubectl  describe  nodes  node01 | grep -iA5  allocated
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests     Limits
  --------           --------     ------
  cpu                525m (52%)   400m (40%)
  memory             500Mi (26%)  740Mi (39%)
controlplane:~$ 
controlplane:~$ kubectl  get  pod             
NAME                    READY   STATUS    RESTARTS   AGE
test-86b4f688bd-d7c5w   1/1     Running   0          13m
test-86b4f688bd-nzlnv   1/1     Running   0          14m
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl top nodes node01
controlplane:~$ 


2) Namespace Quota:

Compute Quota on namespaces level ?

kubectl  create ns testing

kubectl create quota demo  --hard=memory=400Mi,cpu=400m -n  testing

kubectl describe quota demo -n  testing


apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
  namespace: krnetwork
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      - image: docker.io/nginx
        name: nginx
        resources: 
          requests:
              memory: 100Mi
              cpu: 100m
          limits:
              memory: 200Mi
              cpu: 200m


kubectl create -f  deploy.yml

kubectl  get  quota test  -n krnetwork 

kubectl  edit  quota test  -n  testing

apiVersion: v1
kind: ResourceQuota
metadata:
  creationTimestamp: "2025-05-29T03:35:48Z"
  name: test
  namespace: krnetwork
  resourceVersion: "4500"
  uid: 4ae3e9f9-91a3-407e-aa7a-3ed157319323
spec:
  hard:
    limits.cpu: 800m
    limits.memory: 800Mi
    requests.cpu: 400m
    requests.memory: 400Mi
status:
  hard:
    limits.cpu: 800m
    limits.memory: 800Mi
    requests.cpu: 400m
    requests.memory: 400Mi
  used:
    limits.cpu: 800m
    limits.memory: 800Mi
    requests.cpu: 400m
    requests.memory: 400Mi


LimitRange ?

LimitRange: (Namespace level)

   1. we can define default cpu/memory on namespaces.
   2. max values for the cpus/memory.
   3. min values for the cpus/memory.


vim limitrange.yml

apiVersion: v1
kind: LimitRange
metadata:
   name: xyz
spec: 
   limits:
   - default:  # it is limit value
	memory: 512Mi
     defaultRequest:	#it is requests value
	memory: 256Mi
     type: Container 





apiVersion: v1
kind: LimitRange
metadata:
   name: xyz
spec: 
   limits:
   - default:  # it is limit value
	memory: 512Mi
     defaultRequest:	#it is requests value
	memory: 256Mi
     max:
	memory: 512Mi
     min: 
	memory: 40Mi
     type: Container 



Objective/ResourceQuota:

kubectl  create  ns example

kubectl create quota xyz  --hard=pods=4,services=5  -n  example

kubectl  describe  quota  xyz  -n  example

kubectl  create  deploy  --image=docker.io/nginx --replicas=5  deploy -n example

kubectl  get  pod  -n  example

kubectl  describe  quota xyz  -n  example

kubectl  get  deploy  -n  example

kubectl create quota xyz --hard=pods=4,services=5  -n  example  --dry-run  -o  yaml >  is.yml

vim  is.yml
apiVersion: v1
kind: ResourceQuota
metadata:
  creationTimestamp: null
  name: xyz
  namespace: example
spec:
   count/persistentvolumeclaims: 5
   count/services: 4
   count/secrets: 3
   cpu:  100m
   memory: 200Mi 

:wq!


kubectl  create  -f  is.yml

kubectl describe  -f  is.yml


===========================================================================
ConfigMap and Secret
===========================================================================

Configmap ?

1) It contains service related configgile/variable/parameterfiles.
	Example: nginx/apache/httpd/mysql.

2) It is object API which take place in the etcd.

3) Configmap namespace level object.

4) It map to the container in read-only mode.

5) There are two way for calling map to container.

	1) Variable 
	2) As volume Mounts read-only.

Lab-1:  How to map config file through configmap ?



controlplane:~$ 
controlplane:~$ vim  httpd.conf

<virtualhost *:80>
 ServerAdmin root@localhost
Documentroot	/var/www/html
DirectoryIndex	index.html
ServerName    servera.example.com
</ViratulHost>

:wq!
controlplane:~$ kubectl  create  cm   test-cm   --from-file=httpd.conf 
configmap/test-cm created
controlplane:~$ 
controlplane:~$ kubectl  get  cm
NAME               DATA   AGE
kube-root-ca.crt   1      18d
test-cm            1      8s
controlplane:~$ 
controlplane:~$ kubectl  get  cm   test-cm 
NAME      DATA   AGE
test-cm   1      12s
controlplane:~$ 
controlplane:~$ kubectl  get  cm   test-cm -o  yaml 
apiVersion: v1
data:
  httpd.conf: |+
    <VirtualHost  *:80>
    ServerAdmin root@localhost
    DocumentRoot  /var/www/html
    DirectoryIndex  index.html
    ServerName   servera.example.com
    </VirtualHost>

kind: ConfigMap
metadata:
  creationTimestamp: "2025-05-30T14:42:59Z"
  name: test-cm
  namespace: default
  resourceVersion: "4171"
  uid: fcdc0743-a576-4590-ad18-71d7b54d66a2
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl   create   deployment    web  --image=nginx  --dry-run  -o  yaml   >  deploy.yml
W0530 14:45:30.204304   13030 helpers.go:702] --dry-run is deprecated and can be replaced with --dry-run=client.
controlplane:~$ 
controlplane:~$ vim  deploy.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
        - image: nginx
          name: nginx
          volumeMounts:
            - name: xyz
              mountPath: /etc/config
      volumes:
        - name: xyz
          configMap:
            name: test-cm

:wq!

controlplane:~$
controlplane:~$ kubectl   create  -f  deploy.yml 
controlplane:~$ 
controlplane:~$ kubectl   get   pod
NAME                   READY   STATUS    RESTARTS   AGE
web-696cfff864-q5gr9   1/1     Running   0          99s
controlplane:~$ 
controlplane:~$ kubectl  exec  -it  web-696cfff864-q5gr9 --  bash
root@web-696cfff864-q5gr9:/# 
root@web-696cfff864-q5gr9:/# cat  /etc/config/httpd.conf 
<VirtualHost *:80>
ServerAdmin root@localhost
DocumentRoot  /var/www/html
DirectoryIndex  index.html
ServerName    servera.example.com
Customlog     "logs/access_log"  combined
Errorlog      "logs/error_log"
</VirtualHost>

<Directory  /var/www/html>
require   all  granted
</Directory>

root@web-696cfff864-q5gr9:/# 
root@web-696cfff864-q5gr9:/# exit
exit
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl   get  cm  test-cm 
NAME      DATA   AGE
test-cm   1      6m11s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl   get  cm  test-cm -o  yaml
apiVersion: v1
data:
  httpd.conf: |+
    <VirtualHost *:80>
    ServerAdmin root@localhost
    DocumentRoot  /var/www/html
    DirectoryIndex  index.html
    ServerName    servera.example.com
    Customlog     "logs/access_log"  combined
    Errorlog      "logs/error_log"
    </VirtualHost>

    <Directory  /var/www/html>
    require   all  granted
    </Directory>

kind: ConfigMap
metadata:
  creationTimestamp: "2025-05-31T07:30:06Z"
  name: test-cm
  namespace: default
  resourceVersion: "3316"
  uid: abc2c0f3-5b0d-42c7-a79c-4d8d6aedf7f3
controlplane:~$ 

kubectl create  cm  demo-cm --from-literal=proxyip=http://192.168.0.2

controlplane:~$ vim  deploy.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
        - image: nginx
          name: nginx
	  envFrom:
	    - configMapRef:  
		name: demo-cm


:wq!


kubectl  create  -f  deploy.yml



controlplane:~$ 
controlplane:~$ vim deploy.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: webserver
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      - image: nginx
        name: nginx
        envFrom:
          - configMapRef:
              name: demo-cm

:wq!

controlplane:~$ 
controlplane:~$ kubectl  create  -f  deploy.yml 
deployment.apps/webserver created
controlplane:~$ 
controlplane:~$ kubectl get  pod
NAME                         READY   STATUS    RESTARTS   AGE
web-696cfff864-q5gr9         1/1     Running   0          20m
webserver-756fd548d4-mjvsb   1/1     Running   0          10s
controlplane:~$ 
controlplane:~$ kubectl   exec  -it  webserver-756fd548d4-mjvsb   -- bash
root@webserver-756fd548d4-mjvsb:/# 
root@webserver-756fd548d4-mjvsb:/# env
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_PORT=443
HOSTNAME=webserver-756fd548d4-mjvsb
PROXY_IP=http://192.168.0.2
PWD=/
PKG_RELEASE=1~bookworm
HOME=/root
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
DYNPKG_RELEASE=1~bookworm
NJS_VERSION=0.8.10
TERM=xterm
SHLVL=1
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
NGINX_VERSION=1.27.5
NJS_RELEASE=1~bookworm
_=/usr/bin/env
root@webserver-756fd548d4-mjvsb:/# 
root@webserver-756fd548d4-mjvsb:/# 

root@webserver-756fd548d4-mjvsb:/# 
root@webserver-756fd548d4-mjvsb:/# exit
exit
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  get  cm  
NAME               DATA   AGE
demo-cm            1      3m6s
kube-root-ca.crt   1      19d
test-cm            1      24m
controlplane:~$ 
controlplane:~$ kubectl  get  cm   demo-cm   -o  yaml
apiVersion: v1
data:
  PROXY_IP: http://192.168.0.2
kind: ConfigMap
metadata:
  creationTimestamp: "2025-05-31T07:51:24Z"
  name: demo-cm
  namespace: default
  resourceVersion: "5126"
  uid: be2c2823-9495-4d16-9d2e-45b9f141cc98
controlplane:~$ 
controlplane:~$ 
controlplane:~$
controlplane:~$ kubectl edit cm  demo-cm 
configmap/demo-cm edited
controlplane:~$
controlplane:~$ kubectl   get  cm   
NAME               DATA   AGE
demo-cm            1      9m22s
kube-root-ca.crt   1      19d
test-cm            1      30m
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl   get  cm   demo-cm  -o  yaml
apiVersion: v1
data:
  PROXY_IP: http://192.168.0.200
kind: ConfigMap
metadata:
  creationTimestamp: "2025-05-31T07:51:24Z"
  name: demo-cm
  namespace: default
  resourceVersion: "5813"
  uid: be2c2823-9495-4d16-9d2e-45b9f141cc98
controlplane:~$ 


controlplane:~$ 
controlplane:~$ kubectl   get  pod
NAME                         READY   STATUS    RESTARTS   AGE
web-696cfff864-q5gr9         1/1     Running   0          26m
webserver-756fd548d4-mjvsb   1/1     Running   0          5m52s
controlplane:~$ 
controlplane:~$ kubectl  delete   pod  webserver-756fd548d4-mjvsb
pod "webserver-756fd548d4-mjvsb" deleted
controlplane:~$ 
controlplane:~$ kubectl   get  pod
NAME                         READY   STATUS    RESTARTS   AGE
web-696cfff864-q5gr9         1/1     Running   0          26m
webserver-756fd548d4-rf5jj   1/1     Running   0          8s
controlplane:~$ 
controlplane:~$ kubectl  exec  -it   webserver-756fd548d4-rf5jj   --  bash
root@webserver-756fd548d4-rf5jj:/#  env
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_PORT=443
HOSTNAME=webserver-756fd548d4-rf5jj
PROXY_IP=http://192.168.0.200
PWD=/
PKG_RELEASE=1~bookworm
HOME=/root
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
DYNPKG_RELEASE=1~bookworm
NJS_VERSION=0.8.10
TERM=xterm
SHLVL=1
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
NGINX_VERSION=1.27.5
NJS_RELEASE=1~bookworm
_=/usr/bin/env
root@webserver-756fd548d4-rf5jj:/# 
root@webserver-756fd548d4-rf5jj:/# exit
exit
controlplane:~$ 
controlplane:~$ 

==========================================================================================================



Secrets
________________________________________

1. It contains sensitive information

	1. password/credential
	2. certificate
	3. token
	4. registries credentials.

Types of secrets

1. Generic: SSH Keys/ Token / Password/
2. TLS: Certificate store
3. Docker-registry: It contains image registries server credential.

Attach to containers:

1. Env variable:
2. As volume Mounts:



Example-1:


controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl   create   deployment   mysql  --image=mysql:5.6  --dry-run  -o yaml  >  deploy.yml
W0531 18:26:34.583548   19435 helpers.go:702] --dry-run is deprecated and can be replaced with --dry-run=client.
controlplane:~$ 
controlplane:~$ 
controlplane:~$ vim  deploy.yml 


apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: mysql
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: mysql
    spec:
      containers:
      - image: mysql:5.6
        name: mysql
        env:
          - name: MYSQL_ROOT_PASSWORD
            value: redhat

:wq!


controlplane:~$ kubectl   create  -f  deploy.yml 
deployment.apps/mysql created
controlplane:~$ 
controlplane:~$ kubectl  get  pod
NAME                    READY   STATUS              RESTARTS   AGE
mysql-6499c788c-gh6s9   0/1     ContainerCreating   0          4s
controlplane:~$ 
controlplane:~$ kubectl  get  pod
NAME                    READY   STATUS    RESTARTS   AGE
mysql-6499c788c-gh6s9   1/1     Running   0          10s
controlplane:~$ 
controlplane:~$ kubectl   exec  -it  mysql-6499c788c-gh6s9   --  bash
root@mysql-6499c788c-gh6s9:/# 
root@mysql-6499c788c-gh6s9:/# mysql
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
root@mysql-6499c788c-gh6s9:/# 
root@mysql-6499c788c-gh6s9:/# mysql  -u  root -predhat
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.6.51 MySQL Community Server (GPL)

Copyright (c) 2000, 2021, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
mysql> exit
Bye
root@mysql-6499c788c-gh6s9:/# 
root@mysql-6499c788c-gh6s9:/# 
root@mysql-6499c788c-gh6s9:/# exit
exit
controlplane:~$ 
controlplane:~$ 






controlplane:~$ kubectl   create   secret   generic   gensec  --from-literal=mypass=redhat
secret/gensec created
controlplane:~$ 
controlplane:~$ kubectl   get  secrets 
NAME     TYPE     DATA   AGE
gensec   Opaque   1      11s
controlplane:~$ 
controlplane:~$ kubectl   create   secret   generic   mysecret  --from-literal=mypass=redhat   --from-literal=userpass=rhel
secret/mysecret created
controlplane:~$ 
controlplane:~$ kubectl   get  secrets 
NAME       TYPE     DATA   AGE
gensec     Opaque   1      2m13s
mysecret   Opaque   2      3s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl   get  secrets  mysecret -o  yaml  
apiVersion: v1
data:
  mypass: cmVkaGF0
  userpass: cmhlbA==
kind: Secret
metadata:
  creationTimestamp: "2025-05-31T18:34:48Z"
  name: mysecret
  namespace: default
  resourceVersion: "6158"
  uid: 4e4f4a0c-fb1d-46df-b92f-b76d4e676be2
type: Opaque
controlplane:~$ 
controlplane:~$ kubectl   get  secrets  gensec -o  yaml  
apiVersion: v1
data:
  mypass: cmVkaGF0
kind: Secret
metadata:
  creationTimestamp: "2025-05-31T18:32:38Z"
  name: gensec
  namespace: default
  resourceVersion: "5973"
  uid: 8d3fb063-13fe-470b-a0ac-6dc6bfb6fd7d
type: Opaque
controlplane:~$ 
controlplane:~$ 
controlplane:~$ echo  cmVkaGF0  |  base64  -d
redhatcontrolplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl   create   deployment   mysql  --image=docker.io/mysql:5.6  --dry-run  -o yaml  >  deploy.yml
W0531 18:54:25.606790   19476 helpers.go:702] --dry-run is deprecated and can be replaced with --dry-run=client.
controlplane:~$ 
controlplane:~$ vim  deploy.yml

kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: mysql
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: mysql
    spec:
      containers:
      - image: mysql:5.6
        name: mysql
        env:
	  - name: MYSQL_ROOT_PASSWORD
	    valueFrom:
		secretKeyRef:
			name: mysecret
			key: mypass


:wq!

controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl   create  -f  deploy.yml 
deployment.apps/mysql created
controlplane:~$ 
controlplane:~$ kubectl  get  pod
NAME                     READY   STATUS    RESTARTS   AGE
mysql-74b6985dcb-sdgqd   1/1     Running   0          72s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  exec -it  mysql-74b6985dcb-sdgqd   --  bash
root@mysql-74b6985dcb-sdgqd:/# 
root@mysql-74b6985dcb-sdgqd:/# mysql  -uroot -predhat
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.51 MySQL Community Server (GPL)

Copyright (c) 2000, 2021, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
mysql> exit
Bye
root@mysql-74b6985dcb-sdgqd:/# 
root@mysql-74b6985dcb-sdgqd:/# 











controlplane:~$ ssh-keygen
controlplane:~$ 
controlplane:~$ kubectl  create  secret   generic   ssh-keys  --from-file=id_rsa=.ssh/id_rsa   --from-file=id_rsa.pub=.ssh/id_rsa.pub
secret/ssh-keys created
controlplane:~$ 
controlplane:~$ kubectl  get  secrets  ssh-keys  -o  yaml 
apiVersion: v1
data:
  id_rsa: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcEFJQkFBS0NBUUVBeDQzTXVqRElGaDl3L0hJNmVwLzN2dFdsdWM0aXllc0oyamFTS3pvQUw2aGFmVHNNCnpPQnN5S0JUK3hQTFk3ekxCRFYvU3VtY2FHckxDOEJReGlENE4wa1VrZWFhTUdhTEU2ZWw1WndmQ25wZ1lkdm8KMENVK0t0cjJlVTZ4OXlleEdMNUlIbmdSckYzaUg2RGlVNVd5ZW8veThqWjJPTHFrTnpBdS9palZUQVNNcEFJSwo1WGlJUzNyWVJmTGlIdEE1R0dTT2F0OGFNQm9WaUpQTGFXM0dPZC8wQXArZkNKaTlvcytpdkJua2x6ZnZMOERICjZVNnhaTVV6WHVOWnBVek9UNjdvTUNmd1oxRmx4eHViVE9EcitWc01RY3N3ZVFJckYyZDB3U3pLM1ZBS2RhekEKOUNSL2s1c3JDbXFvZEUrQjFlaktlUERTeHVBa1FnZkgyN2hHTFFJREFRQUJBb0lCQVFDbXNYdlgrSkhiY1E0Mwp3cys0eVZIeGRwU0dmbnd2QVloc1lQTis2bWRuYUJHVzFMSGZub1VnbmxVb3dpTS9RR0N5Tm1JTDJOTlBvZzNVCjFpUmpMUDFMYVYrbWRPUk9GQXFQTTVid0U1Y1djSjE4Z1crY0Y5QXppazhqakNvN1ZFdXFhS0RqMnRhZ2pwZ3EKWllGSjR4MkJKaWdDVHA0NHl5Z0w5ZEw3ZW02YW5TSjE5aXhwQ1loRVkwYUtjVERxK2hrcjM0Yk1OaFpGS3l5bwpiaGVEKzZwSXdITy9SaUtBaUhYSnlFcUFoYkVWeE9oMThSOVlSL2xxekJ1dlhEYlhaV1J6bURFWEJGRHpjTnlGCkwzZXRnQUpnZ2hnV25INm5OV1dvSG16Y1VubWlKUEpkU09mRW1LT2tjck1mSDVNbDFIN2ltSUFhZUpPamRCREcKUUlBQmRIVjlBb0dCQVB1eTVWcFJrSzdEa0VZV25tOVZHbVRSdGgwNmtQaFFjQ2llVUtCMlhZVnQ0WExtTGNqMgoxa3A0ZWlDayt4bHg1dmcyQ1plVllwclRuM0NzY1BZSm5paSthQm9ZQi9JVUR5a1BudHVqenhyZHBRZDFBczQyCmtZVy93MFRnMWpwdGVNeUlpbW1HcDVCQzNNREJlSEljYXNvK21EYVBFdGhkWWg4NjA0bjYvbFN6QW9HQkFNcjIKeVRvTE5lS2FhYlEwcTkrWkp2cXAwdEM4RE5Kbm1yRHM5ak8zai9Xb1JONnZCWU14dFMxZkhtOVc2c2xRc1diSApzUGFnUURJMzlKUVNxU0RJTWJHcmJFdTc3bWdyQm41dnEzejZRNjRVdFdBVXpHL1hOc09ndXZxaVRUL1cxMkFJCm14QU9pUXpBZUtYY1k1T21ubzV2Wi9KQWVodUZCY2lRSkRKRml5bWZBb0dCQUsrTlVTYXpxU1FieHE3NzY3bGYKTkMrSmpXbkpoWCtNS3c1SmpaYTl0L0R1ZU1aNUFRb2dEOHFWU1NrbnFQTlFYSFpXeWszdHBYNnpoNHRBMnZEZwpPRzNXa0NsVEJZZTBzZlpEck5zbzM3U3ZUd0ZxZlhCeTR3RFVoVSt2T2Z3NmlueXQ1MHcrM1BSampqRHJRQ1RtCjZSenlsNXZHSXJEQ1FVUGk5ZFJST2I2OUFvR0Fma3lmMzd6R0pXTE5VRlJPZCsvSDc4ZnA1ZlovVEJjb2pkeUgKUUp1RHUreWdIcXB2RmJyQzhaNzFRM2VrMXUyZDEwV0FlNWd4R283elpZbFBNWlVpUjF0VElyV3BuNnpXZDZ0cAozWFdtMUJjUzN0MVNjbTVNSitSSE5sdjFFanJRLzNPYmVweHc4RHg5VjBrVHRmZkRSd05RZmRGZHF1NVdNUDV4Clo4OGEwQ1VDZ1lBaDBHWGNZZkM0c0syQ0FTUHkxYUQ1RU84Wkwzbkh0UE50NHRvMjRjVlFHeVZ3OUlUa2JPV3YKaXpyRmNlSFp3WXZDQnM0S0N0Sy9jdlFGdDNOd1JDZ0pmZE0wakJEbWNva2ZvYTlFTGg3S1AzYlQyc2toenY3UgpaMGZoNmpBbUxGRTlQTTlRcFluUU5WWktoT3FFemRLT0lTTUE1Yy95VXdOOHhLb2FJTHNhbWc9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
  id_rsa.pub: c3NoLXJzYSBBQUFBQjNOemFDMXljMkVBQUFBREFRQUJBQUFCQVFESGpjeTZNTWdXSDNEOGNqcDZuL2UrMWFXNXppTEo2d25hTnBJck9nQXZxRnA5T3d6TTRHeklvRlA3RTh0anZNc0VOWDlLNlp4b2Fzc0x3RkRHSVBnM1NSU1I1cG93Wm9zVHA2WGxuQjhLZW1CaDIralFKVDRxMnZaNVRySDNKN0VZdmtnZWVCR3NYZUlmb09KVGxiSjZqL0x5Tm5ZNHVxUTNNQzcrS05WTUJJeWtBZ3JsZUloTGV0aEY4dUllMERrWVpJNXEzeG93R2hXSWs4dHBiY1k1My9RQ241OEltTDJpejZLOEdlU1hOKzh2d01mcFRyRmt4VE5lNDFtbFRNNVBydWd3Si9CblVXWEhHNXRNNE92NVd3eEJ5ekI1QWlzWFozVEJMTXJkVUFwMXJNRDBKSCtUbXlzS2FxaDBUNEhWNk1wNDhOTEc0Q1JDQjhmYnVFWXQgcm9vdEB1YnVudHUK
kind: Secret
metadata:
  creationTimestamp: "2025-05-31T19:07:46Z"
  name: ssh-keys
  namespace: default
  resourceVersion: "6660"
  uid: e84f037c-7d04-4319-9f7e-955e1d675f93
type: Opaque
controlplane:~$ 
controlplane:~$ 

controlplane:~$ kubectl  run  test  --image=nginx  --dry-run  -o  yaml  > pod         
W0531 19:12:18.547401   28455 helpers.go:702] --dry-run is deprecated and can be replaced with --dry-run=client.
controlplane:~$ 
controlplane:~$ 
controlplane:~$ vim  pod 
 
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: test
  name: test
spec:
  containers:
  - image: nginx
    name: test
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
  volumes:
  - name: foo
    secret:
      secretName: ssh-keys

:wq!

controlplane:~$ 
controlplane:~$ kubectl  create  -f  pod 
controlplane:~$ 
controlplane:~$ kubectl  get  pod
NAME                     READY   STATUS    RESTARTS   AGE
test                     1/1     Running   0          9s
controlplane:~$ 
controlplane:~$ kubectl  exec -it  test  -- bash
root@test:/# 
root@test:/# ls -l   /etc/foo
total 0
lrwxrwxrwx 1 root root 13 May 31 19:18 id_rsa -> ..data/id_rsa
lrwxrwxrwx 1 root root 17 May 31 19:18 id_rsa.pub -> ..data/id_rsa.pub
root@test:/# 
root@test:/# ls    /etc/foo
id_rsa  id_rsa.pub
root@test:/# 
root@test:/# cat   /etc/foo/id_rsa
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAx43MujDIFh9w/HI6ep/3vtWluc4iyesJ2jaSKzoAL6hafTsM
zOBsyKBT+xPLY7zLBDV/SumcaGrLC8BQxiD4N0kUkeaaMGaLE6el5ZwfCnpgYdvo
0CU+Ktr2eU6x9yexGL5IHngRrF3iH6DiU5Wyeo/y8jZ2OLqkNzAu/ijVTASMpAIK
5XiIS3rYRfLiHtA5GGSOat8aMBoViJPLaW3GOd/0Ap+fCJi9os+ivBnklzfvL8DH
6U6xZMUzXuNZpUzOT67oMCfwZ1FlxxubTODr+VsMQcsweQIrF2d0wSzK3VAKdazA
9CR/k5srCmqodE+B1ejKePDSxuAkQgfH27hGLQIDAQABAoIBAQCmsXvX+JHbcQ43
ws+4yVHxdpSGfnwvAYhsYPN+6mdnaBGW1LHfnoUgnlUowiM/QGCyNmIL2NNPog3U
1iRjLP1LaV+mdOROFAqPM5bwE5cWcJ18gW+cF9Azik8jjCo7VEuqaKDj2tagjpgq
ZYFJ4x2BJigCTp44yygL9dL7em6anSJ19ixpCYhEY0aKcTDq+hkr34bMNhZFKyyo
bheD+6pIwHO/RiKAiHXJyEqAhbEVxOh18R9YR/lqzBuvXDbXZWRzmDEXBFDzcNyF
L3etgAJgghgWnH6nNWWoHmzcUnmiJPJdSOfEmKOkcrMfH5Ml1H7imIAaeJOjdBDG
QIABdHV9AoGBAPuy5VpRkK7DkEYWnm9VGmTRth06kPhQcCieUKB2XYVt4XLmLcj2
1kp4eiCk+xlx5vg2CZeVYprTn3CscPYJnii+aBoYB/IUDykPntujzxrdpQd1As42
kYW/w0Tg1jpteMyIimmGp5BC3MDBeHIcaso+mDaPEthdYh8604n6/lSzAoGBAMr2
yToLNeKaabQ0q9+ZJvqp0tC8DNJnmrDs9jO3j/WoRN6vBYMxtS1fHm9W6slQsWbH
sPagQDI39JQSqSDIMbGrbEu77mgrBn5vq3z6Q64UtWAUzG/XNsOguvqiTT/W12AI
mxAOiQzAeKXcY5Omno5vZ/JAehuFBciQJDJFiymfAoGBAK+NUSazqSQbxq7767lf
NC+JjWnJhX+MKw5JjZa9t/DueMZ5AQogD8qVSSknqPNQXHZWyk3tpX6zh4tA2vDg
OG3WkClTBYe0sfZDrNso37SvTwFqfXBy4wDUhU+vOfw6inyt50w+3PRjjjDrQCTm
6Rzyl5vGIrDCQUPi9dRROb69AoGAfkyf37zGJWLNUFROd+/H78fp5fZ/TBcojdyH
QJuDu+ygHqpvFbrC8Z71Q3ek1u2d10WAe5gxGo7zZYlPMZUiR1tTIrWpn6zWd6tp
3XWm1BcS3t1Scm5MJ+RHNlv1EjrQ/3Obepxw8Dx9V0kTtffDRwNQfdFdqu5WMP5x
Z88a0CUCgYAh0GXcYfC4sK2CASPy1aD5EO8ZL3nHtPNt4to24cVQGyVw9ITkbOWv
izrFceHZwYvCBs4KCtK/cvQFt3NwRCgJfdM0jBDmcokfoa9ELh7KP3bT2skhzv7R
Z0fh6jAmLFE9PM9QpYnQNVZKhOqEzdKOISMA5c/yUwN8xKoaILsamg==
-----END RSA PRIVATE KEY-----
root@test:/# 
root@test:/# cat   /etc/foo/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDHjcy6MMgWH3D8cjp6n/e+1aW5ziLJ6wnaNpIrOgAvqFp9OwzM4GzIoFP7E8tjvMsENX9K6ZxoassLwFDGIPg3SRSR5powZosTp6XlnB8KemBh2+jQJT4q2vZ5TrH3J7EYvkgeeBGsXeIfoOJTlbJ6j/LyNnY4uqQ3MC7+KNVMBIykAgrleIhLethF8uIe0DkYZI5q3xowGhWIk8tpbcY53/QCn58ImL2iz6K8GeSXN+8vwMfpTrFkxTNe41mlTM5PrugwJ/BnUWXHG5tM4Ov5WwxByzB5AisXZ3TBLMrdUAp1rMD0JH+TmysKaqh0T4HV6Mp48NLG4CRCB8fbuEYt root@ubuntu
root@test:/# 

root@test:/# exit
exit
controlplane:~$ 
controlplane:~$ 







openssl req -x509 -sha256 -nodes -days 365  -newkey rsa:4096 -keyout private.key -out certificate.crt



controlplane:~$ openssl req -x509 -sha256 -nodes -days 365  -newkey rsa:4096 -keyout private.key -out certificate.crt
........+..........+..+.+........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...+.+...+......+..............+.+..+...+.+.....+.+...............+.....+...+..........+.........+..+.........+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.........+........+...+.......+...+.........+...........+..........+...+.........+..+....+..+....+........+...+...+....+...........+.+.....+.........+.+......................................................+.........+..................+...............+...+.....+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
....+...+...+....+.....+.+.....+..................+.........+...+......................+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..+...........+....+......+........+......+.......+.....+.........+.+.........+........+.......+......+...+..+...+.+......+.........+.....+....+..+.......+...+..+.......+...+...+...............+...+..+....+...........+...+....+.....+.+.....+.+...+..+....+.....+.+.....+......+...+....+...........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+....+.....+......+....+...+..+.+...+.....+..........+.........+..+...+.+......+.........+......+.....+.+...+..............+...............+.........................+..+........................+.......+...+..+...+.......+......................................+....+......+..+............+...+.........+....+..+.........................+..+............+.....................+.......+......+.....+........................+.+.....+.......+..+.+......+......+..................+......+.....+.............+..+.............+...+..+..........+........+...+....+...+.....+.......+..+...+......+.......+........+.........+.....................+..........+......+.....+................+..............+....+.....+............+.........+.............+........+...+......+....+.................+.......+...+.................+.............+..+..................+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:India
String too long, must be at most 2 bytes long
Country Name (2 letter code) [AU]:IN
State or Province Name (full name) [Some-State]:DELHI
Locality Name (eg, city) []:DELHI
Organization Name (eg, company) [Internet Widgits Pty Ltd]:KR
Organizational Unit Name (eg, section) []:NETWORK
Common Name (e.g. server FQDN or YOUR name) []: Rohit
Email Address []:rohit@gmailc.om
controlplane:~$ 
controlplane:~$ ls
certificate.crt  filesystem  private.key
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  create  secret  tls   demo-certs  --cert=certificate.crt   --key=private.key 
secret/demo-certs created
controlplane:~$ 
controlplane:~$ 
controlplane:~$ vim   pod 

apiVersion: v1
kind: Pod
metadata:
  labels:
    run: test
  name: test
spec:
  containers:
  - image: nginx
    name: test
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
  volumes:
  - name: foo
    secret:
      secretName: demo-cert

:wq!

controlplane:~$ 
controlplane:~$ kubectl  create  -f  pod 
pod/test created
controlplane:~$ 
controlplane:~$ kubectl  get  pod   
NAME   READY   STATUS    RESTARTS   AGE
test   1/1     Running   0          18s
controlplane:~$ 
controlplane:~$ kubectl  exec  -it  test  --  bash
root@test:/# 
root@test:/# ls  /etc/foo
tls.crt  tls.key
root@test:/# 
root@test:/# 



kubectl  create secret docker-registry  regsec  --docker-server  docker.io  --docker-username=rohitrawat891997  --docker-password=Rohit@12345

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: docker.io/nginx
      imagePullSecrets:
      - name: regcred





================================================================================================
Volume:
================================================================================================


Persistent Volume : 

      1)  NAS 
      2) SAN
      3) Local 

Volumes resources:

 1) PV: persistent volume : it contains volume related  information:
                                    Storage Server/volume/mode/AccessMode/claimpolicy.

    1.  cluster scope level. 
    2. AccessMode:                                           EBS/EFS
              1) readwriteMany
              2) ReadOnlyMany
             3) ReadWriteOnce

    3. claim Policy: 
                        1) Recycle: if claim requests delete then pv it come back  in available mode and files  lost 
                                                     new team member can used the same  pv now .
                         2) Retain:  pv + data will retain  same stage . 
                         3) deleted :   pv + data both lost . 

    4. PV status :
       1)  Available :
       2) Bound :  it has been claimed by  namespace . 
       3) Released:  
       4) failed

	PVC: persistent volume claim ?  :  this is requests which raised from the namespace . 


	Types of volumes provisioning:
        1) static volume: manually 
        2) Dynamic volume: automatically : not supported : 

PV:
PVC:

Step1: PVC  raised ---Pending state--  PV create 
_________________________________________________________
 Storage-----PV---claim--pVC(Namespace)---Deployment -----Pod 

Lab-1: static  Volume  provisioning using NFS (NAS) Server 

Lab-2:  dynamic  Volume  provisioning using NFS Server 

Lab-3: volume  using SAN storage (EBS)

Lab-4-: Local volume  attach to  pod .   Self-place : recording 

Lab-5: StorageClass/CSI (container storage interface )


Lab-1:

root@controlplane:~# vim nfs-pv.yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: nfs
  nfs:
    path: /scratch
    server: 10.0.0.10
    

:wq!

	OR

apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 256Mi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /home/ubuntu/two-tier-flask-app/mysqldata        #This is your host path where your data will be stored. Make sure to create mysqldata directory in mentioned path


:wq!

root@controlplane:~# 
root@controlplane:~# 
 root@controlplane:~# kubectl get pv
 NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAG                   ECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
 firstvol   5Gi        RWX            Recycle          Available                                             <unset>                          6m44s
root@controlplane:~#
root@controlplane:~# kubectl  create ns example
root@controlplane:~#
root@controlplane:~# vim pvc.yml

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: web-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi


:wq!

 root@controlplane:~#
 root@controlplane:~#
 root@controlplane:~#
 root@controlplane:~# kubectl create -f pvc.yml  -n example
 persistentvolumeclaim/web-pvc created
 root@controlplane:~#
 root@controlplane:~# kubectl get pvc  -n example
 NAME      STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
 web-pvc   Pending                                                     <unset>                 7s



 root@controlplane:~# kubectl get pv
 NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
   K8s Page 4   
NAME       
RWX           
 Recycle          
CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
 firstvol   5Gi        
Available                          
root@controlplane:~#
 root@controlplane:~# kubectl delete pv firstvol
 persistentvolume "firstvol" deleted
 root@controlplane:~#
 root@controlplane:~# vim  pvc.yml
 root@controlplane:~# vim nfs-pv.yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: nfs
  nfs:
    path: /scratch
    server: 10.0.0.10
    

:wq!


 root@controlplane:~#
 root@controlplane:~# 
 root@controlplane:~#
 root@controlplane:~# kubectl create -f nfs-pv.yml
 persistentvolume/firstvol created
 root@controlplane:~#
 root@controlplane:~# kubectl get pvc  -n example
 <unset>                         
 12m
 NAME      STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
 web-pvc   Bound    firstvol   10Gi        RWX
                            
root@controlplane:~#
root@controlplane:~# kubectl get pv
root@controlplane:~#
root@controlplane:~#
 root@controlplane:~# kubectl create  deploy  webserver --image=nginx --dry-run -o yaml > deploy.yml
 W0616 12:35:55.298620    5198 helpers.go:704] --dry-run is deprecated and can be replaced with --dry-run=client.
 root@controlplane:~#
 root@controlplane:~#
 root@controlplane:~# vim deploy.ym

apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nfs-nginx
  template:
    metadata:
      labels:
        app: nfs-nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          volumeMounts:
            - name: logs
              mountPath: /usr/share/nginx/html
      volumes:
        - name: logs
          persistentVolumeClaim:
             claimName: web-pvc

:wq!

 root@controlplane:~# kubectl create -f deploy.yml  -n example
 deployment.apps/webserver created
 root@controlplane:~#
 root@controlplane:~#
 root@controlplane:~#
 root@controlplane:~# kubectl get po -n example
 NAME                      
  READY   STATUS    RESTARTS   AGE
 webserver-c7c749577-ghfsp   1/1     Running   0          
root@controlplane:~#
 6s
 root@controlplane:~# kubectl exec  -it webserver-c7c749577-ghfsp  bash  -n example
 kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
 root@webserver-c7c749577-ghfsp:/#
 root@webserver-c7c749577-ghfsp:/# mount |  grep nfs
 10.0.0.10:/scratch on /usr/share/nginx/html type nfs4 (rw,relatime,vers=4.2,rsize=524288,wsize=524288,namlen=255,hard,proto=tcp,timeo=
 600,retrans=2,sec=sys,clientaddr=10.0.0.11,local_lock=none,addr=10.0.0.10)
 root@webserver-c7c749577-ghfsp:/# cd /usr/share/nginx/html
 K8s Page 5   
root@webserver-c7c749577-ghfsp:/# cd /usr/share/nginx/html
 root@webserver-c7c749577-ghfsp:/usr/share/nginx/html#
 root@webserver-c7c749577-ghfsp:/usr/share/nginx/html# touch abc{1..10}
 root@webserver-c7c749577-ghfsp:/usr/share/nginx/html#
 root@webserver-c7c749577-ghfsp:/usr/share/nginx/html# exit
 exit
 root@controlplane:~#
 root@controlplane:~# kubectl delete pod webserver-c7c749577-ghfsp  -n example
 pod "webserver-c7c749577-ghfsp" deleted
 root@controlplane:~#
 root@controlplane:~#
 root@controlplane:~# kubectl get po -n example
 NAME                      
  READY   STATUS    RESTARTS   AGE
 webserver-c7c749577-jcj6h   1/1     Running   0          
root@controlplane:~#
  root@controlplane:~# kubectl exec -it webserver-c7c749577-jcj6h  bash -n example
 kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
 root@webserver-c7c749577-jcj6h:/#
 root@webserver-c7c749577-jcj6h:/# ls  /usr/share/nginx/html/
 abc1  abc10  abc2  abc3  abc4  abc5  abc6  abc7  abc8  abc9
 root@webserver-c7c749577-jcj6h:/# exit
 exit
 root@controlplane:~#
 root@controlplane:~#
 root@controlplane:~# ls  /scratch/
 abc1  abc10  abc2  abc3  abc4  abc5  abc6  abc7  abc8  abc9
 root@controlplane:~#
 root@controlplane:~# cat nfs-pv.yml
 apiVersion: v1
 kind: PersistentVolume
 metadata:
 name: firstvol
 spec:
 capacity:
 storage: 10Gi
 accessModes:- ReadWriteMany
 nfs:
 path: /scratch
 server: 10.0.0.10
 persistentVolumeReclaimPolicy: Recycle
 root@controlplane:~#
 root@controlplane:~#
 root@controlplane:~#
 root@controlplane:~#
 root@controlplane:~# cat pvc.yml
 apiVersion: v1
 kind: PersistentVolumeClaim
 metadata:
 name: web-pvc
 spec:
 accessModes:- ReadWriteMany
 resources:
 requests:
 storage: 8Gi
 root@controlplane:~#
 root@controlplane:~#
 root@controlplane:~#



Question-1: Volume Policy ?

Static volume

 1. Recycle
 2. retain

Dynamic Volume

Delete 

Question-2:

Question-3: PV size 100%

Noted: static Provisioning cannot extend

Question-4: can be attach multiple volume in single deployment ? Yes: 

Dynamic Provisioning in the kubernetes using NFS (NAS) & (SAN)

Step-1: Storage agent / Storage Provisioned / CSI : container storage interface: Image
Step-2: Service Acccount/Role Access /
Step-3: StorageClass ----Provsioners(CSI)-----Storage Server

Deployment--pvc--storageClass---CSI---PV

VOLUMEBINDINGMODE:

 1) Immediate: Prod
 2) WaitForConsumer: Test Kuberenetes



root@k8s-master:~# cat   nfs-subdir-external-provisioner/deploy/rbac.yaml 
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: nfs-client-provisioner-runner
rules:
  - apiGroups: [""]
    resources: ["nodes"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["persistentvolumes"]
    verbs: ["get", "list", "watch", "create", "delete"]
  - apiGroups: [""]
    resources: ["persistentvolumeclaims"]
    verbs: ["get", "list", "watch", "update"]
  - apiGroups: ["storage.k8s.io"]
    resources: ["storageclasses"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["events"]
    verbs: ["create", "update", "patch"]
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: run-nfs-client-provisioner
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: ClusterRole
  name: nfs-client-provisioner-runner
  apiGroup: rbac.authorization.k8s.io
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["endpoints"]
    verbs: ["get", "list", "watch", "create", "update", "patch"]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: Role
  name: leader-locking-nfs-client-provisioner
  apiGroup: rbac.authorization.k8s.io
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# cat   nfs-subdir-external-provisioner/deploy/class.yaml 
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nfs-client
provisioner: nfsagent 
parameters:
  archiveOnDelete: "false"
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# cat   nfs-subdir-external-provisioner/deploy/deployment.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nfs-client-provisioner
  labels:
    app: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
spec:
  replicas: 1
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: nfs-client-provisioner
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccountName: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          image: registry.k8s.io/sig-storage/nfs-subdir-external-provisioner:v4.0.2
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: nfsagent
            - name: NFS_SERVER
              value: 172.31.64.19
            - name: NFS_PATH
              value: /scratch
      volumes:
        - name: nfs-client-root
          nfs:
            server: 172.31.64.19
            path: /scratch
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# cat   pvc.yml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: web-pvc
spec:
  storageClassName: nfs-client
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi

root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# cat  deploy.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: krnetwork
  name: krnetwork
spec:
  replicas: 1
  selector:
    matchLabels:
      app: krnetwork
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: krnetwork
    spec:
      volumes:
        - name: demo
          persistentVolumeClaim:
            claimName: web-pvc 
      containers:
      - image: docker.io/nginx
        name: nginx
        volumeMounts:
          - name: demo
            mountPath: /usr/share/nginx/html/
root@k8s-master:~# 
root@k8s-master:~# cat  sc.yml 
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  creationTimestamp: "2025-07-11T11:07:23Z"
  name: nfs-client
  resourceVersion: "11861"
  uid: fbe5780a-807a-4281-a76e-ede583102362
parameters:
  archiveOnDelete: "false"
provisioner: nfsagent
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# kubectl get  pod
NAME                                      READY   STATUS    RESTARTS   AGE
krnetwork-7d94fd6d6c-cr4tf                1/1     Running   0          8m8s
nfs-client-provisioner-59c5964454-b2wgt   1/1     Running   0          11m
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# kubectl  get  sc
NAME         PROVISIONER   RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
nfs-client   nfsagent      Delete          WaitForFirstConsumer   true                   11m
root@k8s-master:~# 
root@k8s-master:~# kubectl get  pvc
NAME      STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
web-pvc   Bound    pvc-a801f07d-c9af-454e-bee8-b6a11cde37be   1Gi        RWX            nfs-client     <unset>                 9m49s
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# kubectl describe  deploy  krnetwork
Name:                   krnetwork
Namespace:              default
CreationTimestamp:      Fri, 11 Jul 2025 11:42:30 +0000
Labels:                 app=krnetwork
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=krnetwork
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=krnetwork
  Containers:
   nginx:
    Image:        docker.io/nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:
      /usr/share/nginx/html/ from demo (rw)
  Volumes:
   demo:
    Type:          PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:     web-pvc
    ReadOnly:      false
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   krnetwork-7d94fd6d6c (1/1 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  7m38s  deployment-controller  Scaled up replica set krnetwork-7d94fd6d6c from 0 to 1
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# kubectl get  pod  
NAME                                      READY   STATUS    RESTARTS   AGE
krnetwork-7d94fd6d6c-cr4tf                1/1     Running   0          9m4s
nfs-client-provisioner-59c5964454-b2wgt   1/1     Running   0          12m
root@k8s-master:~# 
root@k8s-master:~# kubectl  exec -it  krnetwork-7d94fd6d6c-cr4tf -- bash
root@krnetwork-7d94fd6d6c-cr4tf:/# cd  /usr/share/nginx/html/
root@krnetwork-7d94fd6d6c-cr4tf:/usr/share/nginx/html# ls 
abc1  abc11  abc12  abc13  abc14  abc15  abc2  abc3  abc4  abc5
root@krnetwork-7d94fd6d6c-cr4tf:/usr/share/nginx/html# 
root@krnetwork-7d94fd6d6c-cr4tf:/usr/share/nginx/html# exit
exit
root@k8s-master:~# ls /scratch/default-web-pvc-pvc-a801f07d-c9af-454e-bee8-b6a11cde37be/
abc1  abc11  abc12  abc13  abc14  abc15  abc2  abc3  abc4  abc5
root@k8s-master:~# 


_____________________________________________
Amazon Storage: API Based:

Access key
Secret Key:

Kubernetes Side: Secrets . ?

How to attach local volume in the pod ? 

[csi driver kubernetes aws -- search on google to perform this task]

kubectl create secret generic aws-secret  --namespace kube-system  --from-literal key_id=AKIAQMEY5ZQRHLWVCYMX    --from-literal access_key=IU0zs7f88rTTrJPFyLFTWNicFI3hWJ+qOrFqH2qO

https://get.helm.sh/helm-v3.17.4-linux-arm64.tar.gz

tar -xzf  helm-v3.17.4-linux-arm64.tar.gz

mv  linux-arm64/helm    /bin

helm repo add aws-ebs-csi-driver https://kubernetes-sigs.github.io/aws-ebs-csi-driver

helm repo update

helm repo list

helm upgrade --install aws-ebs-csi-driver \
    --namespace kube-system \
    aws-ebs-csi-driver/aws-ebs-csi-driver

kubectl get sc

kubectl get CSIDriver

kubectl get CSINode

cat   sc.yml

apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: xyz
provisioner: ebs.csi.aws.com
parameters:
	type: gp3
#volumeBindingMode: WaitForFirstConsumer

:wq!

kubectl create  -f  sc.yml

kubectl get  sc


cat  pvc.yml

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  storageClassName: xyz
# volumeMode:  Filesystem
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi

:wq!

kubectl  create  -f  pvc.yml  

kubectl get pvc

root@controlplane:~# cat  pod.yml 
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
root@controlplane:~# 

kubectl create -f pod.yml

kubectl get pod




kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-ebs-csi-driver

================================================================================
Pod Networking ? 
================================================================================

1. Pod IP

2. PodA to PODB communication same worker/remote node pods

3. Pod want to communicate with Infra network ? Yes 


Pod Network Works CNI concept in the kubernetes


CNI: container network interface .

1. It is software define network solution which provide l3 layer networking on the worker Node side.

2. Types of CNI flavor >

1. Weave
2. Flannel
3. Calico 


1. It configure l3 layer networking in the os .
2. it Provide overlay networking between worker Nodes.
3. It create overlay network using vxlan concept .
4. Each worker has seprate l3 switch with seprate subnet .
5. Calico distribute the route table in the cluster using BGP protocol .


Pod Network ?: 172.16.0.0/16
blockSize: 26
Node Network ? 10.0.0.0/24


1. When we planning for the cluster u need calico network ?

 1. Pod subnet should not overlap with Infra.
 2. it should be private .
 3. Number of worker nodes. (subnet )
 4. how many ip address u need on each worker Nodes.

kubeadm init  --pod-network-cidr=172.16.0.0/16

root@controlplane:~# history
 1 clear
 2 kubectl get ippool
 3 kubectl get ippool default-ipv4-ippool -o yaml
 4 kubectl get node -o wide
 5 kubectl api-resources
 6 kubectl get ipamblocks
 7 kubectl get pod -o wide -n kube-system | grep calico-node
 8 kubectl create deploy test --image=nginx --replica=4
 9 kubectl create deploy test --image=nginx --replicas=4
 10 kubectl get po -o wide
 11 kubectl get ipamblocks
 12 kubectl get po -o wide
 13 kubectl exec -it test-7955cf7657-ljnh5 bash
 14 route -n
 15 ip route
 16 ip a s tunl0
 17 kubectl api-resources
 18 kubectl get IPAMConfig
 19 kubectl get IPAMConfig default -o yaml
 20 clear
 21 curl -O -L https://github.com/projectcalico/calicoctl/releases/download/v3.16.4/calicoctl
 22 ls
 23 chmod +x calicoctl
 24 mv calicoctl /bin
 25 mkdir -p /etc/calico
 26 vim /etc/calico/calicoctl.cfg

apiVersion: projectcalico.org/v3
kind: CalicoAPIConfig
metadata: 
spec:
   datastoreType: "kubernetes"
   kubeconfig: "/etc/kubernetes/admin.conf"

:wq!


 27 calicoctl ippam show
 28 calicoctl ipam show
 29 calicoctl get ippool
 30 kubectl get ippool
 31 calicoctl ipam show --show-blocks
 32 kubeadm init --help	# kubeadm  init  --pod-network-cidr=172.16.0.0/16
 33 vim /home/vagrant/calico.yaml

- name: CALICO_IPV4POOL_CIDR   #Uncomment this line.
  value: "192.168.0.10"        #Uncomment this line.

:wq!

 34 clear
 35 calicoctl get ippool
 36 calicoctl get ippool default-ipv4-ippool -oyaml > is.yml
 37 vim is.yml

apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: demovol
spec:
  blocksize: 28
  cidr: 192.168.22.0/24 
  ipipMode: Always
  natOutgoing: true
  nodeSelector: all()
  vxlanMode: Never

:wq!


 38 calicoctl apply -f is.yml
 39 calicoctl get ippool
 40 kubectl delete deploy --all
 41 kubectl create deploy demo --mage=nginx
 42 kubectl get po -owide
 43 calicoctl get ippool default-ipv4-ippool -oyaml > is.yml
 44 vim is.yml

apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: demovol
spec:
  blocksize: 28
  cidr: 192.168.22.0/24 
  ipipMode: Always
  disabled: true
  natOutgoing: true
  nodeSelector: all()
  vxlanMode: Never

:wq!

 45 kubectl apply -f is.yml
 46 calicoctl apply -f is.yml
 47 vim is.yml
 48 kubectl create -f is.yml
 49 calicoctl apply -f is.yml
 50 kubectl create deploy test --image=nginx
 51 kubectl get pod -owide
 52 calicoctl ipam show --show-blocks
 53 ip route | grep tunl0
 54 kubectl get pod
 55 kubectl get pod -o wide
 56 ip route get 172.16.196.130
 57 ip route get 192.168.22.49
 58 ip a s tunl0



We can placed new subnet as well after the installatiion ?

Service Discovery
Load Balancers
External Client want to communicate pods.
DNS Roles.
Ingress Controllers/mettalb
Network Policy







Service Discovery
________________________________________________


What is the service IP ?

 1. it the proxy ip of the micro-svc
 2. frontend service now it service ip of backen-micro svc.
 3. service ip giving by proxy service.
 4. proxy service basically running on each worker Nodes.
 5. proxy service has seprae Ip pool . Which allocate seprate ip for each micro-service.
 6. service I t is the static ip of the services.
 7. service ip is the namespace scope level.
 8. service it assocate with the pod Ip based on level .
 9. service ip automatically discover pod ip. Even if get deleted , new pod ip automatically attached
 with Service Ip.
 10. service ip can distribute load across the Pods . It is acting like private load Balancer in the
kubernetes .
 11. This ip only accessible within cluster .
 12. It store inside the Iptables . 


Node Pool - 10.0.0.0/16
Pod Pool - 172.16.0.0/16
Service IP Pool or Proxy Pool - 172.17.0.0/16

Note: overlaping

 64 kubectl get pod -n kube-system | grep proxy
 65 kubectl get cm -n kube-system
 66 kubectl get cm -n kube-system kubeadm-config -o yaml
 67 kubectl get deploy
 68 kubectl get pod -o wide
 69 kubectl expose deploy demo --port=80
 70 kubectl get service
 71 kubectl describe service demo
 72 curl 172.17.62.153:80
 73 kubectl get pod
 74 kubectl exec -it demo-664dc5dfd4-k4j82 bash
 75 kubectl exec -it test-7955cf7657-lp9mf bash
 76 kubectl delete pod demo-664dc5dfd4-k4j82
 77 kubectl get pod
 78 kubectl get pod -o wide
 79 kubectl get service
 80 kubectl describe service demo | grep -i end
 81 kubectl exec -it test-7955cf7657-lp9mf bash
 82 kubectl get service
 83 kubectl delete deploy demo
 84 kubectl get service
 85 kubectl describe service demo | grep -i end
 86 kubectl exec -it test-7955cf7657-lp9mf bash
 87 kubectl create deploy demo --image=nginx
 88 kubectl describe service demo | grep -i end
 89 kubectl delete deploy demo
 90 kubectl create deploy ram --image=nginx --dry-run -o yaml > deploy.yml
  91 vim deploy.yml
 92 kubectl describe service demo | grep -i end
 93 kubectl create -f deploy.yml
 94 kubectl describe service demo | grep -i end
 95 kubectl describe service demo | grep -i selector
 96 kubectl get pod --show-labels
 97 kubectl edit service demo
 98 kubectl describe service demo | grep -i selector
 99 kubectl describe service demo | grep -i end
 100 kubectl label pod ram-664dc5dfd4-gjksd color=red
 101 kubectl describe service demo | grep -i end
 102 kubectl label pod test-7955cf7657-lp9mf color=red
 103 kubectl describe service demo | grep -i end
 104 kubectl get pod
 105 kubectl exec -it ram-664dc5dfd4-gjksd bash
 106 kubectl exec -it test-7955cf7657-lp9mf bash
 107 kubectl get service
 108 curl 172.17.62.153
 109 kubectl delete deploy --all
 110 kubectl delete service demo
 111 vim deploy.yml
 112 kubectl create -f deploy.yml
 113 cat > service.yml
 114 vim service.yml

apiVersion: v1
kind: Service
metadata:
  name: order-web
spec:
  selector:
	color: blue
  ports:
    - protocol: TCP
      port: 8081  # Proxy Port any port. it need to pass frontend micro
      targetPort: 80  #  Pod port

:wq!



 115 kubectl create -f service.yml
 116 kubectl describe service order-web
 117 kubectl delete service order-web
 118 kubectl expose deploy ram --port=8081 --target-port=80 --name=my-service
 119 kubectl get service
 120 curl 172.17.49.184:8081
 121 ip a s
 122 iptables -t nat -L PREROUTING | column -t
 123 kubectl get service
 124 iptables -t nat -L KUBE-SVC-FXIYY6OHUSNBITIX -n | column -t
 125 kubectl get pod -owide
 126 kubectl scale --replicas=2 deploy ram
 127 iptables -t nat -L KUBE-SVC-FXIYY6OHUSNBITIX -n | column -t
 128 kubectl get pod -owide
 129 history
 



CORE-DNS
__________________________________________________

ui.default.svc.cluster.local
Service.namespace.svc.cluster.local

svc.cluster.local : cluster private domain

Note:
 
1) all the service ip can resolve through the name.

2) Core-DNS it provide name resolution services.



 
vim  service.yml

apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376

:wq!


kubectl  create  -f  service.yml
kubectl  get  service my-service
kubectl delete service my-service
kubectl create -f  service.yml
kubectl get service  my-service

kubectl get pod -n kube-system | grep -i dns
kubectl get service -n kube-system
kubectl run ui --image=nginx
kubectl expose pod ui --port=80
kubectl get service

kubectl run backend --image=nginx
kubectl get  pod

kubectl exec -it backend bash
cat /etc/resolve.conf
apt update -y ; apt install dnsutils -y
nslookup ui.default

exit

kubectl get  pod -n kube-system
kubectl get  deploy -n kube-system
kubectl  scale  --replicas=3 deploy coredns  -n  kube-system
kubectl get service -n kube-system









controlplane:~$ kubectl   get  po
NAME      READY   STATUS    RESTARTS   AGE
backend   1/1     Running   0          51s
ui        1/1     Running   0          74s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl   get  service  
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
backend      ClusterIP   10.111.245.117   <none>        80/TCP    16s
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   25d
ui           ClusterIP   10.104.12.111    <none>        80/TCP    44s
controlplane:~$ 
controlplane:~$ kubectl  exec   -it  backend   --  bash
root@backend:/# 
root@backend:/# nslookup  ui
bash: nslookup: command not found
root@backend:/# 
root@backend:/# apt  update -y  &&  apt install  dns-utils -y
Get:1 http://deb.debian.org/debian bookworm InRelease [151 kB]


root@backend:/# 
root@backend:/# 
root@backend:/# nslookup  ui
;; Got recursion not available from 10.96.0.10
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   ui.default.svc.cluster.local
Address: 10.104.12.111
;; Got recursion not available from 10.96.0.10

root@backend:/# 
root@backend:/# 
root@backend:/# cat   /etc/resolv.conf 
search default.svc.cluster.local svc.cluster.local cluster.local
nameserver 10.96.0.10
options ndots:5
root@backend:/# 
root@backend:/# curl  ui
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
root@backend:/# exit
exit
controlplane:~$ 
controlplane:~$ kubectl  exec   -it  ui   --  bash
root@ui:/# 
root@ui:/# echo   UI  >  /usr/share/nginx/html/index.html 
root@ui:/# 
root@ui:/# exit
exit
controlplane:~$ 
controlplane:~$ kubectl  exec   -it  backend   --  bash
root@backend:/# 
root@backend:/# curl  ui
UI
root@backend:/# 
root@backend:/# 
root@backend:/# exit
exit
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 



Service discovery:

1) Service IP
2) Core-DNS

Mirco-1  ---- CORE-DNS ---- Lookup ---- Service-1 ---- Mirco-2

Proxy ?
Core-DNS ?



Expose Service IP
__________________________________________


How to expose kubernetes service for the external world ?

	1) Port Forwarding
		1) Pod : not recommended


kubectl  create  deploy  test  --image=nginx --dry-run -o yaml > deploy.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: test
  labels:
    app: nginx
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
        ports:
        - containerPort: 80
	  hostPort: 8881

kubectl create  -f  deploy.yml
kubectl  get  pod
netstat  -tunlp  |  grep -w 8881


		2) Service IP


kubectl  create  deploy  test  --image=nginx --dry-run -o yaml > deploy.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: test
  labels:
    app: nginx
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

kubectl create -f  deploy.yml
kubectl expose deploy test  --port-80  --type=NodePort
kubectl  get  service
kubectl  get  pod
kubectl exec -it <pod name>  -- bash
echo pod1 > /usr/share/nginx/html/index.html
exit
kubectl exec -it <pod name>  -- bash
echo pod2 > /usr/share/nginx/html/index.html
exit

curl localhost:port_number
curl localhost:port_number
curl localhost:port_number
curl localhost:port_number




Service IP: NodePort Service

What are the types of services:
  1) ClusterIP
  2) NodePort: Portforwarding
  3) LoadBalancer: Hold

===========================================================================================
Ingress Controller
===========================================================================================

1. NodePort:
2. Ingress Controllers:
	1. it is application layer load balancer.
	2. it will run inside the kubernetes clusters.
	3. Now no need to configure multiple externel load balancer for each micro-service.
	4. it optimize cost/operation work.
	5. balancer level control now it come under k8s administrator.
	6. Ingress Controller node it use nginx for the balancer rules .

 	https://tickets.paytm.com/trains/: Targaet -- pod --SVC1--NodePort

https://tickets.paytm.com/bus/

Path routing


Application vs Network Load balacer ?

Network Load balancer in the kubernetes ?

Mettal LB ?

Https/https based: Ingress Controllers : Application Laod balancer ?

TCP/IP based Traffic: SSH based: Network Load balancer ? 
_______________________________________________________________________________
Ingress Controller Installation


  4  git clone https://github.com/nginx/kubernetes-ingress.git --branch v5.0.0
    5  cd kubernetes-ingress
    6  git  branch  -a
    7  git  checkout remotes/origin/release-5.0
    8  ls
    9  kubectl apply -f deployments/common/ns-and-sa.yaml
   10  kubectl  get  ns
   11  kubectl   get  sa
   12  kubectl   get  sa  -n  nginx-ingress 
   13  kubectl apply -f deployments/rbac/rbac.yaml
   14  kubectl apply -f deployments/rbac/ap-rbac.yaml
   15  kubectl apply -f deployments/rbac/apdos-rbac.yaml
   16  kubectl apply -f examples/shared-examples/default-server-secret/default-server-secret.yaml
   17  kubectl apply -f deployments/common/nginx-config.yaml
   18  kubectl apply -f deployments/common/ingress-class.yaml
   19  kubectl apply -f https://raw.githubusercontent.com/nginx/kubernetes-ingress/v5.0.0/deploy/crds.yaml
   20  kubectl apply -f https://raw.githubusercontent.com/nginx/kubernetes-ingress/v5.0.0/deploy/crds-nap-waf.yaml
   21  kubectl apply -f https://raw.githubusercontent.com/nginx/kubernetes-ingress/v5.0.0/deploy/crds-nap-dos.yaml
   22  kubectl apply -f deployments/deployment/nginx-ingress.yaml
   23  historykubectl get  pod -n  nginx-ingress

controlplane:~/kubernetes-ingress$ kubectl get  pod -n  nginx-ingress -owide
NAME                            READY   STATUS    RESTARTS   AGE     IP            NODE     NOMINATED NODE   READINESS GATES
nginx-ingress-5f7d87867-r6fgm   1/1     Running   0          4m45s   192.168.1.4   node01   <none>           <none>
controlplane:~/kubernetes-ingress$ 
controlplane:~/kubernetes-ingress$ 
controlplane:~/kubernetes-ingress$ 
controlplane:~/kubernetes-ingress$ kubectl edit deploy  nginx-ingress  -n nginx-ingress 
deployment.apps/nginx-ingress edited

# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "2"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-ingress","namespace":"nginx-ingress"},"spec":{"replicas":1,"selector":{"matchLabels":{"app":"nginx-ingress"}},"template":{"metadata":{"labels":{"app":"nginx-ingress","app.kubernetes.io/name":"nginx-ingress"}},"spec":{"automountServiceAccountToken":true,"containers":[{"args":["-nginx-configmaps=$(POD_NAMESPACE)/nginx-config","-report-ingress-status","-external-service=nginx-ingress"],"env":[{"name":"POD_NAMESPACE","valueFrom":{"fieldRef":{"fieldPath":"metadata.namespace"}}},{"name":"POD_NAME","valueFrom":{"fieldRef":{"fieldPath":"metadata.name"}}}],"image":"nginx/nginx-ingress:5.0.0","imagePullPolicy":"IfNotPresent","name":"nginx-ingress","ports":[{"containerPort":80,"name":"http"},{"containerPort":443,"name":"https"},{"containerPort":8081,"name":"readiness-port"},{"containerPort":9113,"name":"prometheus"}],"readinessProbe":{"httpGet":{"path":"/nginx-ready","port":"readiness-port"},"periodSeconds":1},"resources":{"requests":{"cpu":"100m","memory":"128Mi"}},"securityContext":{"allowPrivilegeEscalation":false,"capabilities":{"add":["NET_BIND_SERVICE"],"drop":["ALL"]},"runAsNonRoot":true,"runAsUser":101}}],"securityContext":{"seccompProfile":{"type":"RuntimeDefault"}},"serviceAccountName":"nginx-ingress"}}}}
  creationTimestamp: "2025-06-14T07:50:44Z"
  generation: 2
  name: nginx-ingress
  namespace: nginx-ingress
  resourceVersion: "6420"
  uid: bdba635c-b8ea-471e-b138-de7afcd20461
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: nginx-ingress
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx-ingress
        app.kubernetes.io/name: nginx-ingress
    spec:
      automountServiceAccountToken: true
      hostNetwork: true                   ########### here we add hostNetwork ##############
      containers:
      - args:
        - -nginx-configmaps=$(POD_NAMESPACE)/nginx-config
        - -report-ingress-status
        - -external-service=nginx-ingress
        env:
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
        - name: POD_NAME
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: metadata.name
        image: nginx/nginx-ingress:5.0.0
        imagePullPolicy: IfNotPresent
        name: nginx-ingress
        ports:
        - containerPort: 80
          name: http
          protocol: TCP
        - containerPort: 443
          name: https
          protocol: TCP
        - containerPort: 8081
          name: readiness-port
          protocol: TCP
        - containerPort: 9113
          name: prometheus
          protocol: TCP
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /nginx-ready
            port: readiness-port
            scheme: HTTP
          periodSeconds: 1
          successThreshold: 1
          timeoutSeconds: 1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            add:
            - NET_BIND_SERVICE
            drop:
            - ALL
          runAsNonRoot: true
          runAsUser: 101
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext:
        seccompProfile:
          type: RuntimeDefault
      serviceAccount: nginx-ingress
      serviceAccountName: nginx-ingress
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 1
  conditions:
  - lastTransitionTime: "2025-06-14T07:50:56Z"
    lastUpdateTime: "2025-06-14T07:50:56Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2025-06-14T07:50:45Z"
    lastUpdateTime: "2025-06-14T07:58:30Z"
    message: ReplicaSet "nginx-ingress-bfbcb9744" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 2
  readyReplicas: 1
  replicas: 1
  updatedReplicas: 1
controlplane:~/kubernetes-ingress$ 
controlplane:~/kubernetes-ingress$ kubectl get  pod -n  nginx-ingress -owide
NAME                            READY   STATUS    RESTARTS   AGE   IP           NODE     NOMINATED NODE   READINESS GATES
nginx-ingress-bfbcb9744-pz9bt   1/1     Running   0          4s    172.30.2.2   node01   <none>           <none>
controlplane:~/kubernetes-ingress$ 
controlplane:~/kubernetes-ingress$ 
controlplane:~/kubernetes-ingress$ cd


Example No: 1
=========================================================

controlplane:~$ 
controlplane:~$ kubectl  create  deployment  paytm  --image=nginx 
deployment.apps/paytm created
controlplane:~$ 
controlplane:~$ kubectl  create  deployment  bus  --image=nginx 
deployment.apps/bus created
controlplane:~$ 
controlplane:~$ kubectl  create  deployment  flight  --image=nginx 
deployment.apps/flight created
controlplane:~$ 
controlplane:~$ kubectl  get  pod
NAME                      READY   STATUS    RESTARTS   AGE
bus-66b7759bb5-9p5w9      1/1     Running   0          19s
flight-5b4ff9cc97-ndn4h   1/1     Running   0          9s
paytm-5b7fb86448-j5msl    1/1     Running   0          26s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  expose  deployment  bus   --port=80
service/bus exposed
controlplane:~$ kubectl  expose  deployment  paytm   --port=80
service/paytm exposed
controlplane:~$ kubectl  expose  deployment  flight   --port=80
service/flight exposed
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  get  service
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
bus          ClusterIP   10.99.16.91      <none>        80/TCP    22s
flight       ClusterIP   10.109.178.222   <none>        80/TCP    8s
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   3d23h
paytm        ClusterIP   10.98.218.67     <none>        80/TCP    14s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl   get  ingressclass                     
NAME    CONTROLLER                     PARAMETERS   AGE
nginx   nginx.org/ingress-controller   <none>       22m
controlplane:~$ 
controlplane:~$ 
controlplane:~$ cat is.yml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: bus
spec:
  ingressClassName: nginx
  rules:
  - host: "foo.bus.com"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: bus
            port:
              number: 80

:wq!
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ vim   /etc/hosts

 10.111.207.241  foo.bus.com

:wq!

controlplane:~$ 
controlplane:~$ 
controlplane:~$ curl   http://foo.bus.com
I am bus service
controlplane:~$ 
controlplane:~$ 
controlplane:~$ cat  is.yml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: flight
spec:
  ingressClassName: nginx
  rules:
  - host: "foo.flight.com"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: flight
            port:
              number: 80

controlplane:~$ vim   /etc/hosts

 10.111.207.241  foo.flight.com

:wq!

controlplane:~$ 
controlplane:~$ 
controlplane:~$ curl   http://foo.flight.com
I am flight service
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ cat  is.yml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: paytm
spec:
  ingressClassName: nginx
  rules:
  - host: "foo.paytm.com"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: paytm
            port:
              number: 80

controlplane:~$ vim   /etc/hosts

 10.111.207.241  foo.paytm.com

:wq!

controlplane:~$ 
controlplane:~$ 
controlplane:~$ curl   http://foo.paytm.com
I am paytm service
controlplane:~$ 


Example-2 

controlplane:~$ cat  ingress.yml 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: paytm
spec:
  ingressClassName: nginx
  rules:
  - host: "paytm.foo.com"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: paytm
            port:
              number: 80
  
      - pathType: Prefix
        path: "/bus"
        backend:
          service:
            name: bus
            port:
              number: 80

      - pathType: Prefix
        path: "/flight"
        backend:
          service:
            name: flight
            port:
              number: 80

controlplane:~$ 
controlplane:~$ vim  /etc/hosts

172.30.2.2	paytm.foo.com

:wq!

controlplane:~$ 
controlplane:~$ kubectl   get  pod
NAME                      READY   STATUS    RESTARTS   AGE
bus-66b7759bb5-84gcd      1/1     Running   0          12m
flight-5b4ff9cc97-g4vqz   1/1     Running   0          12m
paytm-5b7fb86448-lj6r8    1/1     Running   0          12m
controlplane:~$ 
controlplane:~$ kubectl  exec -it  flight-5b4ff9cc97-g4vqz   -- bash
root@flight-5b4ff9cc97-g4vqz:/# cd  /usr/share/nginx/html/
root@flight-5b4ff9cc97-g4vqz:/usr/share/nginx/html# mkdir  flight
root@flight-5b4ff9cc97-g4vqz:/usr/share/nginx/html# 
root@flight-5b4ff9cc97-g4vqz:/usr/share/nginx/html# 
root@flight-5b4ff9cc97-g4vqz:/usr/share/nginx/html# mv  index.html   flight/ 
root@flight-5b4ff9cc97-g4vqz:/usr/share/nginx/html# 
root@flight-5b4ff9cc97-g4vqz:/usr/share/nginx/html# 
root@flight-5b4ff9cc97-g4vqz:/usr/share/nginx/html# 
root@flight-5b4ff9cc97-g4vqz:/usr/share/nginx/html# exit  
exit
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  exec -it bus-66b7759bb5-84gcd   -- bash
root@bus-66b7759bb5-84gcd:/# cd  /usr/share/nginx/
root@bus-66b7759bb5-84gcd:/usr/share/nginx# cd html/
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# 
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# 
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# 
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# mkdir  bus 
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# 
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# 
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# mv  index.html   bus/
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# 
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# 
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# 
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# 
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# exxit
bash: exxit: command not found
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# 
root@bus-66b7759bb5-84gcd:/usr/share/nginx/html# exit
exit
controlplane:~$ 
controlplane:~$ curl  paytm.foo.com/flight/
I am fight server
controlplane:~$ curl  paytm.foo.com/bus/
I am bus server
controlplane:~$ 
controlplane:~$ curl  paytm.foo.com     
I am paytm server
controlplane:~$ 

=====================================================================================

Network policy :

Set of rules:

Rules:

 1) ingress : incoming
 2) egress : outging ?

There are type checks/criterias ?

1) Pod Label
2) Namespace label
3) Source Network
4) Port Based

Noted: we can define at a time multiple contexts:

Formate:

 and
 or

Contexts1 dev

Context2 color=red

Noted: Policy determine the pod bases on label.

Lab-1: NS : web DB App

controlplane:~$ kubectl  get   pod -o  wide
NAME    READY   STATUS    RESTARTS   AGE     IP            NODE     NOMINATED NODE   READINESS GATES
app     1/1     Running   0          8m48s   192.168.1.5   node01   <none>           <none>
mysql   1/1     Running   0          7m14s   192.168.1.6   node01   <none>           <none>
web     1/1     Running   0          8m55s   192.168.1.4   node01   <none>           <none>
controlplane:~$ 
controlplane:~$
controlplane:~$ vim net.yaml

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: mysql-from-app
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: mysql
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: app
    ports:
    - protocol: TCP
      port: 3306

:wq!

controlplane:~$
controlplane:~$ kubectl create -f  net.yaml
controlplane:~$
controlplane:~$ kubectl label pod app  tier=app
controlplane:~$ kubectl label pod mysql  tier=mysql
controlplane:~$ kubectl label pod web   tier=web
controlplane:~$
controlplane:~$ kubectl  get  pod  --show-labels 
NAME    READY   STATUS    RESTARTS   AGE     LABELS
app     1/1     Running   0          8m58s   run=app,tier=app
mysql   1/1     Running   0          7m24s   run=mysql,tier=mysql
web     1/1     Running   0          9m5s    run=web,tier=web
controlplane:~$ 
controlplane:~$ kubectl exec -it  web -- bash
root@web:/# telnet 192.168.1.6 3306
Trying 192.168.1.6...
^C
root@web:/# exit
controlplane:~$
controlplane:~$ 

Scenarios: allow db from app not web same namspace

Lab-2: DB from different Namespace App

NS1: DB
NS2: App


controlplane:~$ kubectl create ns krnetwork
controlplane:~$
controlplane:~$ kubectl lable ns krnetwork env=prod
controlplane:~$
controlplane:~$ kubectl run app --image=nginx 
controlplane:~$
controlplane:~$ kubectl label pod app tier=app
controlplane:~$
controlplane:~$ kubectl  get  pod  --show-labels 
NAME    READY   STATUS    RESTARTS   AGE   LABELS
app     1/1     Running   0          16m   run=app,tier=app
mysql   1/1     Running   0          14m   run=mysql,tier=mysql
web     1/1     Running   0          16m   run=web,tier=web
controlplane:~$ 
controlplane:~$ kubectl  get  pod  --show-labels -n  krnetwork 
NAME   READY   STATUS    RESTARTS   AGE   LABELS
app    1/1     Running   0          13m   run=app,tier=app
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  get  pod -owide
NAME    READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
app     1/1     Running   0          15m   192.168.1.4   node01   <none>           <none>
mysql   1/1     Running   0          13m   192.168.1.6   node01   <none>           <none>
web     1/1     Running   0          14m   192.168.1.5   node01   <none>           <none>
controlplane:~$ 
controlplane:~$ vim  net.yml 
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: mysql-from-app
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: mysql
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          env: prod
      podSelector:
        matchLabels:
            tier: app
    ports:
    - protocol: TCP
      port: 3306

:wq!

controlplane:~$ kubectl create -f  net.yml
controlplane:~$ kubectl get  pod  -n   krnetwork 
NAME   READY   STATUS    RESTARTS   AGE
app    1/1     Running   0          12m
controlplane:~$ 
controlplane:~$ kubectl  exec  -it  app  -n  krnetwork -- bash
root@app:/# telnet  192.168.1.6 3306
Trying 192.168.1.6...
Connected to 192.168.1.6.
Escape character is '^]'.
J
5.6.51X>l)"kz"|l+/JfCYwm:mysql_native_password
root@app-57497c5bc8-h8dcm:/# exit
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  get  pod  -n  testing 
NAME                   READY   STATUS    RESTARTS   AGE
app-57497c5bc8-h8dcm   1/1     Running   0          15m
controlplane:~$ 
controlplane:~$ kubectl  exec -it  -n  testing  app-57497c5bc8-h8dcm  -- bash
root@app-57497c5bc8-h8dcm:/# telnet 192.168.1.6 3306
Trying 192.168.1.6...
^C
root@app-57497c5bc8-h8dcm:/# 


Lab-3: DB access from all the pods from Krnetwork namespace?

Lab-4: Example of O R 

Lab-5: NS1:
80 port all the pods should be allow from env=prod tyeps

Create a new NetworkPolicy named allow-port-from-namespace that allows Pods in
the existing namespace my-app to connect to port 8080 of other Pods in the internal
namespace.
Ensure that the new NetworkPolicy:
• does not allow access to Pods not listening on port 8080
• does not allow access from Pods not in namespace internal


controlplane:~$ vim  net.yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: mysql-from-app
  namespace: default
spec:
  podSelector:
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          env: prod
      
    ports:
    - protocol: TCP
      port: 80

:wq!

=====================================================================================

Blue & Green & Canary

1. Canary
	v1: 90%
	v2: 10%

10

Deploy1: Version1:9
Deploy2: Version2:1

Balancers: service ip - selector




1. Blue-green

=====================================================================================

Stateful

root@k8s-master:~# cat  is.yaml 
apiVersion: v1
kind: Service
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  ports:
  - port: 3306
    name: mysql
  clusterIP: None
  selector:
    app: mysql
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: "mysql"
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: docker.io/mysql:5.6
        ports:
        - containerPort: 3306
          name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: redhat
        volumeMounts:
        - name: www
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "nfs-client"
      resources:
        requests:
          storage: 1Gi

root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# kubectl  get  pod  -n  test2
NAME      READY   STATUS    RESTARTS   AGE
mysql-0   1/1     Running   0          108s
mysql-1   1/1     Running   0          4m19s
mysql-2   1/1     Running   0          4m16s
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# kubectl  get  pv  -n  test2
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM               STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
pvc-0c92e790-a8c6-4a95-a3c0-b13d346cfb09   1Gi        RWO            Delete           Bound    test2/www-mysql-1   nfs-client     <unset>                          4m24s
pvc-b1aea151-667c-43ea-93a8-b2ef91cee76a   1Gi        RWO            Delete           Bound    test2/www-mysql-2   nfs-client     <unset>                          4m21s
pvc-e871697f-a3b4-424e-bf0e-c1691a50be13   1Gi        RWO            Delete           Bound    test2/www-mysql-0   nfs-client     <unset>                          4m26s
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# kubectl  get  pvc  -n  test2
NAME          STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
www-mysql-0   Bound    pvc-e871697f-a3b4-424e-bf0e-c1691a50be13   1Gi        RWO            nfs-client     <unset>                 4m31s
www-mysql-1   Bound    pvc-0c92e790-a8c6-4a95-a3c0-b13d346cfb09   1Gi        RWO            nfs-client     <unset>                 4m29s
www-mysql-2   Bound    pvc-b1aea151-667c-43ea-93a8-b2ef91cee76a   1Gi        RWO            nfs-client     <unset>                 4m26s
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# 
root@k8s-master:~# ls  /scratch
test2-www-mysql-0-pvc-e871697f-a3b4-424e-bf0e-c1691a50be13  test2-www-mysql-2-pvc-b1aea151-667c-43ea-93a8-b2ef91cee76a
test2-www-mysql-1-pvc-0c92e790-a8c6-4a95-a3c0-b13d346cfb09
root@k8s-master:~# 
root@k8s-master:~# 








































Deployment
Pod Scheduling
Resource Allocation
LimitRange
ConfigMap
Secrets
PVC/PV configured
Deployment PVC
Service
Ingress Rules
Network Policy

Jenkins Deployment ?

1) Installation
2) Modification
3) Cleanup

Terms:
1) Helm chart: it is the package of all required maifests for any product.
	1) Community based
	2) Custom
2) Helm Repo:
	this is collection of chart. it is centerlized place of chart.

3) Chart directory Arch:
	chart:
	   1) chart.yaml: metadata:/chart version/software/author
 	   2) templates: all the yaml files (deployment,service,quota/cm/secrets)
	   3) values.yml: user can put the value according env.







=======================================================================================
Auto Scaling

1- Pod Autoscaling

1) Min : 2
2) Max : 10
3) Threshold

HPA: Horizontal Pod Autoscaling

Vertical
Horizontal

_____________________________________________________________
Metrics Server ?

#### use this url for metrics installation guide
https://serverfault.com/questions/1153770/installed-metrics-server-in-kubernetes-cluster-but-getting-serviceunavailable

https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

made following changes

- --kubelet-insecure-tls=true
- --kubelet-preferred-address-types=InternalIP

Example:-

controlplane:~$ 
controlplane:~$ kubectl  create   -f   https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
serviceaccount/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
service/metrics-server created
deployment.apps/metrics-server created
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  get  deploy  metrics-server  -n  kube-system 
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
metrics-server   0/1     1            0           56s
controlplane:~$ 
controlplane:~$ kubectl  edit  deploy  metrics-server  -n  kube-system 

      - args:
 	- --kubelet-insecure-tls=true		 ######## add this line here ########
        - --cert-dir=/tmp
        - --secure-port=10250
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        - --metric-resolution=15s

:wq!

controlplane:~$ kubectl  get  pod  -n  kube-system  |  grep metric
metrics-server-66f4bd7f5c-bw6hv           1/1     Running   0               31s
controlplane:~$ 
controlplane:~$ kubectl  get  deploy  metrics-server  -n  kube-system
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
metrics-server   1/1     1            1           5m2s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  top  nodes
NAME           CPU(cores)   CPU(%)   MEMORY(bytes)   MEMORY(%)   
controlplane   95m          9%       1236Mi          58%         
node01         458m         45%      669Mi           35%         
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  top  pod
NAME                    CPU(cores)   MEMORY(bytes)   
test-6bc6b589d7-sn65h   0m           2Mi             
controlplane:~$ 
controlplane:~$ 
controlplane:~$ helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
"kubernetes-dashboard" has been added to your repositories
controlplane:~$ 
controlplane:~$  helm upgrade --install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --create-namespace --namespace kubernetes-dashboard
Release "kubernetes-dashboard" does not exist. Installing it now.
NAME: kubernetes-dashboard
LAST DEPLOYED: Thu Jul 17 06:11:06 2025
NAMESPACE: kubernetes-dashboard
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
*************************************************************************************************
*** PLEASE BE PATIENT: Kubernetes Dashboard may need a few minutes to get up and become ready ***
*************************************************************************************************

Congratulations! You have just installed Kubernetes Dashboard in your cluster.

To access Dashboard run:
  kubectl -n kubernetes-dashboard port-forward svc/kubernetes-dashboard-kong-proxy 8443:443

NOTE: In case port-forward command does not work, make sure that kong service name is correct.
      Check the services in Kubernetes Dashboard namespace using:
        kubectl -n kubernetes-dashboard get svc

Dashboard will be available at:
  https://localhost:8443
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  edit  service  kubernetes-dashboard-kong-proxy -n kubernetes-dashboard

# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: Service
metadata:
  annotations:
    meta.helm.sh/release-name: kubernetes-dashboard
    meta.helm.sh/release-namespace: kubernetes-dashboard
  creationTimestamp: "2025-07-17T05:13:46Z"
  labels:
    app.kubernetes.io/instance: kubernetes-dashboard
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: kong
    app.kubernetes.io/version: "3.8"
    enable-metrics: "true"
    helm.sh/chart: kong-2.46.0
  name: kubernetes-dashboard-kong-proxy
  namespace: kubernetes-dashboard
  resourceVersion: "6161"
  uid: f24185ef-c460-42ac-9a47-4517665a54cc
spec:
  clusterIP: 10.111.25.166
  clusterIPs:
  - 10.111.25.166
  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - name: kong-proxy-tls
    nodePort: 30008			########### Add this line here ###########
    port: 443
    protocol: TCP
    targetPort: 8443
  selector:
    app.kubernetes.io/component: app
    app.kubernetes.io/instance: kubernetes-dashboard
    app.kubernetes.io/name: kong
  sessionAffinity: None
  type: NodePort		########## Set NodePort ##########
tatus:
  loadBalancer: {}

:wq!

controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  get  service  -n  kubernetes-dashboard
NAME                                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
kubernetes-dashboard-api               ClusterIP   10.104.10.206   <none>        8000/TCP        19m
kubernetes-dashboard-auth              ClusterIP   10.99.114.180   <none>        8000/TCP        19m
kubernetes-dashboard-kong-proxy        NodePort    10.111.25.166   <none>        443:30008/TCP   19m		##### here are the changes #####
kubernetes-dashboard-metrics-scraper   ClusterIP   10.110.13.195   <none>        8000/TCP        19m
kubernetes-dashboard-web               ClusterIP   10.97.168.4     <none>        8000/TCP        19m
controlplane:~$ 
controlplane:~$ kubectl  create sa ui -n kube-system 
serviceaccount/ui created
controlplane:~$ 
controlplane:~$ kubectl  create token ui -n kube-system 
eyJhbGciOiJSUzI1NiIsImtpZCI6IjNJdUZIVzhqMGxxWk1XRFlDeWYwOWstT3hIU1o2TlJMVFY4NmFVb09jUjQifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzUyNzM0Mjg1LCJpYXQiOjE3NTI3MzA2ODUsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwianRpIjoiNDBiNWMxZjYtNjQwOC00Zjk2LWEyMWQtMWE2ZjMyMTA3OGEwIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJ1aSIsInVpZCI6IjNhY2ZhNzFkLWU2MTMtNDg4Yi04ZjBmLWI0MGI5OGIyOTQwOCJ9fSwibmJmIjoxNzUyNzMwNjg1LCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06dWkifQ.pfv5W3d2uD_6FZm6tTKF58pYkwhqP_iDxhb7rvVRxV3SAgLf3b_bSMDPRKkDA6eWKC9jL6plptdPHr4dgHS_vndZfmmMNQbLZFo5bnRE87yy3f5WEelba0TyEw4vR73pCNWECZHHU5saDLDCW8meb1cT9Tqufu-TyMOQz4Ml2bVTygpfrKpe6RWMXWEu2pAtjZpeostdvdSynDZRA1jOO_m9ynQVEHZv-yhEtnyoaMQWtFB_90_8wVqXypM999zqF61XE_d_HvA7bOj8blxFnjeYcKSS9-UtfK_W_qEfOPtsGqE8LREg1szsZ5Qvvh3czkzZnSQsBNy0OxGSAuf-yQ
controlplane:~$ 
controlplane:~$ kubectl create clusterrolebinding ui --clusterrole=cluster-admin  --serviceaccount=kube-system:ui
clusterrolebinding.rbac.authorization.k8s.io/ui created
controlplane:~$ 
controlplane:~$ 
controlplane:~$ ls
filesystem  
controlplane:~$ 
controlplane:~$ wget https://k8s.io/examples/application/php-apache.yaml
--2025-07-17 05:47:16--  https://k8s.io/examples/application/php-apache.yaml
Resolving k8s.io (k8s.io)... 34.107.204.206, 2600:1901:0:26f3::
Connecting to k8s.io (k8s.io)|34.107.204.206|:443... connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: https://kubernetes.io/examples/application/php-apache.yaml [following]
--2025-07-17 05:47:17--  https://kubernetes.io/examples/application/php-apache.yaml
Resolving kubernetes.io (kubernetes.io)... 15.197.167.90, 3.33.186.135
Connecting to kubernetes.io (kubernetes.io)|15.197.167.90|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 579 [application/x-yaml]
Saving to: 'php-apache.yaml'

php-apache.yaml                       100%[======================================================================>]     579  --.-KB/s    in 0s      

2025-07-17 05:47:17 (57.8 MB/s) - 'php-apache.yaml' saved [579/579]

controlplane:~$ 
controlplane:~$ ls
filesystem  php-apache.yaml
controlplane:~$ 
controlplane:~$ 
 controlplane:~$ vim  php-apache.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  selector:
    matchLabels:
      run: php-apache
  template:
    metadata:
      labels:
        run: php-apache
    spec:
      containers:
      - name: php-apache
        image: registry.k8s.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 500m
          requests:
            cpu: 200m
---
apiVersion: v1
kind: Service
metadata:
  name: php-apache
  labels:
    run: php-apache
spec:
  ports:
  - port: 80
  selector:
    run: php-apache
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
horizontalpodautoscaler.autoscaling/php-apache autoscaled
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl get hpa php-apache
NAME         REFERENCE               TARGETS       MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   cpu: 0%/50%   1         10        1          18s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
If you don't see a command prompt, try pressing enter.
OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl  get  deploy php-apache 
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
php-apache   1/5     3            1           14m
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl get hpa php-apache
NAME         REFERENCE               TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   cpu: 250%/50%   1         10        5          2m2s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl get  deploy  php-apache 
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
php-apache   1/5     5            1           5m32s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ watch kubectl  get  pod

Every 2.0s: kubectl get pod                                                                                    controlplane: Thu Jul 17 06:23:50 2025

NAME                          READY   STATUS    RESTARTS   AGE
php-apache-6487c65df8-6bc9k   0/1     Pending   0          3m4s
php-apache-6487c65df8-l5h6m   0/1     Pending   0          3m4s
php-apache-6487c65df8-pvtsn   0/1     Pending   0          3m19s
php-apache-6487c65df8-qck9q   1/1     Running   0          7m26s
php-apache-6487c65df8-rpx98   0/1     Pending   0          2m49s

Every 2.0s: kubectl get pod                                                                                    controlplane: Thu Jul 17 06:27:47 2025

NAME                          READY   STATUS    RESTARTS   AGE
php-apache-6487c65df8-qck9q   1/1     Running   0          11m





============================================================
























========================================================
rbac - Role Binding Access Control

Role Based Access Control

Kubernetes-admin : this is the super user .

 1) Authentication:
 	1. certificate Based authentication
 	2. token based
 	3. external identity providers (by default not supported ):

2) Authorization

	# Geenrate private key for admin user
	openssl genrsa -out admin.key 2048

	# Generate CSR for admin user. Note the OU.
	openssl req -new -key admin.key -subj "/CN=admin/O=system:masters" -out admin.csr

	# Sign certificate for admin user using CA servers private key
	openssl x509 -req -in admin.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out admin.crt -days 1000



 lab-1: certificate : 



 94 ls /etc/kubernetes/pki/
 95 mkdir certs
 96 rm -rf certs
 97 cd ce
 98 mkdir certs
 99 cd certs/
 100 cp /etc/kubernetes/pki/ca.crt
 101 cp /etc/kubernetes/pki/ca.crt .
 102 cp /etc/kubernetes/pki/ca.key .
 103 ls
 104 cd
 105 ls -l /etc/kubernetes/admin.conf
 106 rm .kube/config
 107 kubectl get nodes
 108 kubectl config get-contexts
 109 kubectl config get-contexts --kubeconfig=/etc/kubernetes/admin.conf
 110 grep -i user /etc/kubernetes/admin.conf
 111 grep -i cert /etc/kubernetes/admin.conf
 112 ls -l /etc/kubernetes/admin.conf
 113 scp /etc/kubernetes/admin.conf ram@192.168.139.154:/tmp
 114 clear
 115 cd certs/
 116 ls
 117 openssl genrsa -out jeff.key 2048
 118 ls
 119 openssl req -new -key admin.key -subj "/CN=jeff/O=staff" -out jeff.csr
 120 openssl req -new -key jeff.key -subj "/CN=jeff/O=staff" -out jeff.csr
 121 ls
 122 openssl x509 -req -in jeff.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out jeff.crt -days 365
 123 ls
 124 cp /etc/kubernetes/admin.conf jeff.conf
 125 vim jeff.conf
 126 cat jeff.crt
 127 cat jeff.crt | base64 -w0
 128 vim jeff.con
 129 cat jeff.key | base64 -w0
 130 vim jeff.conf
 131 ls
 132 rm -f ca.crt ca.key jeff.crt jeff.csr
  133 ls
 134 rm -f jeff.key
 135 ls
 136 scp jeff.conf ram@192.168.139.154:/tmp
 137 ls /etc/kubernetes/pki/
 138 grep key jeff.conf
 139 grep cert jeff.conf
 140 grep key jeff.conf
 141 cd
 142 cd -
 143 vim jeff.conf
 144 cd
 K8s Page 7
 144 cd
 145 kubectl api-resources
 146 cp /etc/kubernetes/admin.conf .kube/config
 147 kubectl api-resources
 148 kubectl get clusterrole
 149 kubectl create clusterrolebinding read-access --clusterrole=view --user=jeff
 150 kubectl delete clusterrolebinding read-access
 151 kubectl create rolebinding read-access --clusterrole=view --user=jeff -n default
 152 history
root@controlplane:~#
ubuntu@node01:~$ kubectl  config  get-contexts --kubeconfig=/tmp/jeff.conf
CURRENT   NAME   CLUSTER      AUTHINFO           NAMESPACE
          jeff   kubernetes   kubernetes-admin   
ubuntu@node01:~$ 
root@k8s-master:~/certs# cat    jeff.conf 
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJVGVMY3JRQWQ5d293RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBM01UWXhNelV6TXpKYUZ3MHpOVEEzTVRReE16VTRNekphTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURZNXIwUjhoV3Bsazl1MkJnZ3hyNHFESk5zNFNnb0pFdTFkbFZ5emRxMlEwaFIzcXM5aTk5RGNvWSsKRFNKK3RNakdyaE92UUMrU2dQQVViQW9QTlBIQ3hjTzU2T1d2K2V4dGlwdVcxSnZEVnNZK2hIenhBeFpvN293VQpFRm5CbVlVSnZaemNMT2FsUEd3V3FMaGI4RjNHTlJvYThKMjJrcmlOeUViRGZnQnFkcDZtalRaRnliK1k4UjhoCmx3MGUrUDI0cHpOTGg3R2lEa0VjcldpdzFocFNVK0dJTTFvd0ZySlBaeWp1V0NoVTdFWHhidWJ2OVF0MmVBa0UKQVhja3kxUHBkaGJHSUlFSUlqYlhOYXBrR1JQVkhlUHFXV0NieW1qOWlpcjRvK1RyS3RYcHZJNlNrc1FZMkswOQpTSWpYOFMvUmJucGxaUDZjSGsyU0FjSkFKUFdUQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJRWndrMnMzYklaNlNpZU5iT0FObUp1N1VyQmJ6QVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQUQydXZVeDZFMwpqaUNjNmJpT2xyeEMwM01MZTE2aTBvM1B4RWlEQk16WjlyQ1A2cEZpTWxBRGR3cVdrcGpVSDRJMVdjUFB3MlNuCjd0NlMwc01SOU9iS3Yrd21GQ2VUbDUrNlNaclBhaTF4UW5VYXFMNWhGVDAvSDh0QzRTYUhuYlI2b0xCdGdUQXkKeTMxWTljb1kxMUd5QW8rNGFWdzFXcmJRZ3ZJU1NxZk9vL1pQbEhHcHQvdktzQ2hmUFhhRlZXM3diQ1p2QVhkSQpFbkt5WWhJMGNjaWp6b3B3ZnZHZ2RUMGFncUtDTWhiNERVZXFOV2wyeEZadTR6YUV5SXp0bC9xUHVRUGx1Z1VECjJDNjhmazBjcUxRQXQwdHlURHdERkpvNWY3cDVtVS9mOExINUdpQWZscml1YTZxcWlrZksyS09xSFAxVThOdUkKS0R1T1p5RUJVU21aCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://172.31.64.216:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: jeff
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: jeff
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJVGVMY3JRQWQ5d293RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBM01UWXhNelV6TXpKYUZ3MHpOVEEzTVRReE16VTRNekphTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURZNXIwUjhoV3Bsazl1MkJnZ3hyNHFESk5zNFNnb0pFdTFkbFZ5emRxMlEwaFIzcXM5aTk5RGNvWSsKRFNKK3RNakdyaE92UUMrU2dQQVViQW9QTlBIQ3hjTzU2T1d2K2V4dGlwdVcxSnZEVnNZK2hIenhBeFpvN293VQpFRm5CbVlVSnZaemNMT2FsUEd3V3FMaGI4RjNHTlJvYThKMjJrcmlOeUViRGZnQnFkcDZtalRaRnliK1k4UjhoCmx3MGUrUDI0cHpOTGg3R2lEa0VjcldpdzFocFNVK0dJTTFvd0ZySlBaeWp1V0NoVTdFWHhidWJ2OVF0MmVBa0UKQVhja3kxUHBkaGJHSUlFSUlqYlhOYXBrR1JQVkhlUHFXV0NieW1qOWlpcjRvK1RyS3RYcHZJNlNrc1FZMkswOQpTSWpYOFMvUmJucGxaUDZjSGsyU0FjSkFKUFdUQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJRWndrMnMzYklaNlNpZU5iT0FObUp1N1VyQmJ6QVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQUQydXZVeDZFMwpqaUNjNmJpT2xyeEMwM01MZTE2aTBvM1B4RWlEQk16WjlyQ1A2cEZpTWxBRGR3cVdrcGpVSDRJMVdjUFB3MlNuCjd0NlMwc01SOU9iS3Yrd21GQ2VUbDUrNlNaclBhaTF4UW5VYXFMNWhGVDAvSDh0QzRTYUhuYlI2b0xCdGdUQXkKeTMxWTljb1kxMUd5QW8rNGFWdzFXcmJRZ3ZJU1NxZk9vL1pQbEhHcHQvdktzQ2hmUFhhRlZXM3diQ1p2QVhkSQpFbkt5WWhJMGNjaWp6b3B3ZnZHZ2RUMGFncUtDTWhiNERVZXFOV2wyeEZadTR6YUV5SXp0bC9xUHVRUGx1Z1VECjJDNjhmazBjcUxRQXQwdHlURHdERkpvNWY3cDVtVS9mOExINUdpQWZscml1YTZxcWlrZksyS09xSFAxVThOdUkKS0R1T1p5RUJVU21aCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K 
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcEFJQkFBS0NBUUVBMk9hOUVmSVZxWlpQYnRnWUlNYStLZ3lUYk9Fb0tDUkx0WFpWY3MzYXRrTklVZDZyClBZdmZRM0tHUGcwaWZyVEl4cTRUcjBBdmtvRHdGR3dLRHpUeHdzWER1ZWpsci9uc2JZcWJsdFNidzFiR1BvUjgKOFFNV2FPNk1GQkJad1ptRkNiMmMzQ3ptcFR4c0ZxaTRXL0JkeGpVYUd2Q2R0cEs0amNoR3czNEFhbmFlcG8wMgpSY20vbVBFZklaY05Idmo5dUtjelM0ZXhvZzVCSEsxb3NOWWFVbFBoaUROYU1CYXlUMmNvN2xnb1ZPeEY4VzdtCjcvVUxkbmdKQkFGM0pNdFQ2WFlXeGlDQkNDSTIxeldxWkJrVDFSM2o2bGxnbThwby9Zb3ErS1BrNnlyVjZieU8Ka3BMRUdOaXRQVWlJMS9FdjBXNTZaV1QrbkI1TmtnSENRQ1Qxa3dJREFRQUJBb0lCQVFDUXVPUk9wUWh2THl1dworZDZmZi94Q2NrR3IxRS9HK1BETEV6blFjQ29Pa3JZZWFrUTN2S3dyaEs2dG1IWDd0UVNDRElSMTJvM2pZc1ROCjI0aWF2T2pTM01ITHVlb0hrT3AvN0l3RUI3Z21qaStZWnk0Mllha0NPcWljZW94bzhOWWp1YkIvK0RXczZyUDAKS245WVdQazQxeFdrQkFnM1dZNXFpOGNyaVZjZHNPMDRXTlNvZlF5azBiNEFYbzU1eTBsdzM5blhDeVJsc0N1awplc2M0ZGNoL2V2OUxwcHRPWXcvdjdUNEZSVFBhZ3dORTFIR05jUXRSMktJVUVFV09OOWppYnE2VE5vSXRIMjhPClFXWmZxa3ZSUFJXT0VJYUpLbWJ0bzJnd2FxcWZnUFNNbndsWHNDUFJmNDc4VTR0dGFnY0dJODh2azFXdDRzN2oKQUpyUjRISUJBb0dCQU9DUVk4NTE3SkwvN1ZVbGRwbVNZYjBuRUJNS2pIa2ttUFpDYlBuNVZ4U1VVZ0FJS0NwYQozdktNOGZoUnRYZW1nOWVpcjArVmdISWRtSnRaTGlpZ3N6OHVtcU9VcG9NUlUwRGFaNHlPUWxYajN5NGczZU9CClpQSjAzY3gxaGN2elVvWVNQeE80WW0vL0kvaHNKTXA0LzhDTWExK3RDelBtREs4Uis1WFJPM2JSQW9HQkFQZEQKdmx1NGg3ZVh6bmxxbFhPd0NOcmRtSS9lT1ZtODhJM2tuSWtFNHBhR2NNc1MrakVkY21WeHV4Um5DWlI5QVBCRwpqZENyRnhDcWI2ZzYrRUFCaUlsNTVrOU9mQVZ2dWhoSXhORHRFS2dNdTFoVk04M2lNRm9DMmlQQ1BBSGRwSzBPCnZBUTZEU0tLRnc2VHJXU2hJc1hSMUEwR1JQZGlkeTBnRytyeGhBY2pBb0dCQUkvNXd5UEIwM1hFQkVRTkN1Y1gKUlBiakxwa21zRDJUUzBiSCs5cWhGRVdyZW85dXEySE1kUmlKYVRIT0lVeWZRL1JpNTRkYzBNZFpWbGh6YXZhOQpZVzdtMEZxRW1lVjZuQ2YrWDFsc2MvZGhhOXZGVXIza1hmWmRHejlWTWYwNitmUGZpdlYzeUJVS1BlT3VZa0EyCko3TXAxRVc1U205eDFUd0JpWTNhWWdlaEFvR0FXaU5zK3B6TGhPY25VdVhDODdpQmtkL2taTlg3RERhdmJ0cWYKSWVnUS9oU21lN0ZBV2JWRlphdlBLa0hob1Nyc3M2clV1ZlFVNVhOc1h1U1lhU1RsOUUvbTRKUlk2OVp0L2JwUgpYV1IraFBTWE96MWNORHEzcTZpTWd5cWttMzJFTWI1RVZqb2ZYYTcvZHNYcEdOMFJBeEpaL2lHQjlMa0Zmd0xNCmlwcGVxZ2NDZ1lCZDJTZmpXb0FFSnlrWFdQdXpTSEU2bUpQMXB5V2hTRFVwUkRLL3RQRTl5YjU1QWNIVm81bUUKb1ZCOWlJbE5VakEwSitXOVU4ejdkbFQ1SU81b1BhZU4wbWdDcitBZVk3OEV6amk2TmY3ZkY4SngwbE5CZS93QQpNSEpsUmpTazhZUXVXVFJoRktBejBsUjRMZ0JiOE9iYk5NdEFMUitCODFwcXRRbXBGU0dNa2c9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo= 
root@k8s-master:~/certs# 

ubuntu@node01:~$ kubectl config  get-contexts --kubeconfig=/tmp/jeff.conf
CURRENT   NAME   CLUSTER        AUTHINFO   NAMESPACE
          xyz    prod-cluster   jeff       
ubuntu@node01:~$ 
ubuntu@node01:~$ kubectl config  use-context xyz --kubeconfig=/tmp/jeff.conf
Switched to context "xyz".
ubuntu@node01:~$ 
ubuntu@node01:~$ 
_______________________________________________________


1. Role: set of rules

 rules: resources/verbs

 resources: pod/node/deployment/rs/rs/limitrange/pvc/pv

 verbs: list/delete/update/exec/describe/edit/scale/

	1) Default roles: already during installation
	2) Custom Roles: we will cover

Types scopes:
     1) Cluster level :
     2) Namespace level :

2. Rolebinding : role delegate to user :

Note-1: namespace scope role only can bind namespace level.
Note-2: cluster scope role can be used on cluster + namespace level both .


Role-1: pod - create ---Clusterscope

 case-1: ram--role-1
 case-2: ram--prod(namespace)---role-1


controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl get  clusterrole
NAME                                                                   CREATED AT
admin                                                                  2025-06-24T14:06:03Z
calico-kube-controllers                                                2025-06-24T14:06:06Z
calico-node                                                            2025-06-24T14:06:06Z
cluster-admin                                                          2025-06-24T14:06:03Z
edit                                                                   2025-06-24T14:06:03Z
flannel                                                                2025-06-24T14:06:06Z
kubeadm:get-nodes                                                      2025-06-24T14:06:04Z
local-path-provisioner-role                                            2025-06-24T14:08:07Z
system:aggregate-to-admin                                              2025-06-24T14:06:03Z
system:aggregate-to-edit                                               2025-06-24T14:06:03Z
system:aggregate-to-view                                               2025-06-24T14:06:03Z
system:auth-delegator                                                  2025-06-24T14:06:03Z
system:basic-user                                                      2025-06-24T14:06:03Z
system:certificates.k8s.io:certificatesigningrequests:nodeclient       2025-06-24T14:06:03Z
system:certificates.k8s.io:certificatesigningrequests:selfnodeclient   2025-06-24T14:06:03Z
system:certificates.k8s.io:kube-apiserver-client-approver              2025-06-24T14:06:03Z
system:certificates.k8s.io:kube-apiserver-client-kubelet-approver      2025-06-24T14:06:03Z
system:certificates.k8s.io:kubelet-serving-approver                    2025-06-24T14:06:03Z
system:certificates.k8s.io:legacy-unknown-approver                     2025-06-24T14:06:03Z
system:controller:attachdetach-controller                              2025-06-24T14:06:03Z
system:controller:certificate-controller                               2025-06-24T14:06:03Z
system:controller:clusterrole-aggregation-controller                   2025-06-24T14:06:03Z
system:controller:cronjob-controller                                   2025-06-24T14:06:03Z
system:controller:daemon-set-controller                                2025-06-24T14:06:03Z
system:controller:deployment-controller                                2025-06-24T14:06:03Z
system:controller:disruption-controller                                2025-06-24T14:06:03Z
system:controller:endpoint-controller                                  2025-06-24T14:06:03Z
system:controller:endpointslice-controller                             2025-06-24T14:06:03Z
system:controller:endpointslicemirroring-controller                    2025-06-24T14:06:03Z
system:controller:ephemeral-volume-controller                          2025-06-24T14:06:03Z
system:controller:expand-controller                                    2025-06-24T14:06:03Z
system:controller:generic-garbage-collector                            2025-06-24T14:06:03Z
system:controller:horizontal-pod-autoscaler                            2025-06-24T14:06:03Z
system:controller:job-controller                                       2025-06-24T14:06:03Z
system:controller:legacy-service-account-token-cleaner                 2025-06-24T14:06:03Z
system:controller:namespace-controller                                 2025-06-24T14:06:03Z
system:controller:node-controller                                      2025-06-24T14:06:03Z
system:controller:persistent-volume-binder                             2025-06-24T14:06:03Z
system:controller:pod-garbage-collector                                2025-06-24T14:06:03Z
system:controller:pv-protection-controller                             2025-06-24T14:06:03Z
system:controller:pvc-protection-controller                            2025-06-24T14:06:03Z
system:controller:replicaset-controller                                2025-06-24T14:06:03Z
system:controller:replication-controller                               2025-06-24T14:06:03Z
system:controller:resourcequota-controller                             2025-06-24T14:06:03Z
system:controller:root-ca-cert-publisher                               2025-06-24T14:06:03Z
system:controller:route-controller                                     2025-06-24T14:06:03Z
system:controller:selinux-warning-controller                           2025-06-24T14:06:03Z
system:controller:service-account-controller                           2025-06-24T14:06:03Z
system:controller:service-cidrs-controller                             2025-06-24T14:06:03Z
system:controller:service-controller                                   2025-06-24T14:06:03Z
system:controller:statefulset-controller                               2025-06-24T14:06:03Z
system:controller:ttl-after-finished-controller                        2025-06-24T14:06:03Z
system:controller:ttl-controller                                       2025-06-24T14:06:03Z
system:controller:validatingadmissionpolicy-status-controller          2025-06-24T14:06:03Z
system:coredns                                                         2025-06-24T14:06:04Z
system:discovery                                                       2025-06-24T14:06:03Z
system:heapster                                                        2025-06-24T14:06:03Z
system:kube-aggregator                                                 2025-06-24T14:06:03Z
system:kube-controller-manager                                         2025-06-24T14:06:03Z
system:kube-dns                                                        2025-06-24T14:06:03Z
system:kube-scheduler                                                  2025-06-24T14:06:03Z
system:kubelet-api-admin                                               2025-06-24T14:06:03Z
system:monitoring                                                      2025-06-24T14:06:03Z
system:node                                                            2025-06-24T14:06:03Z
system:node-bootstrapper                                               2025-06-24T14:06:03Z
system:node-problem-detector                                           2025-06-24T14:06:03Z
system:node-proxier                                                    2025-06-24T14:06:03Z
system:persistent-volume-provisioner                                   2025-06-24T14:06:03Z
system:public-info-viewer                                              2025-06-24T14:06:03Z
system:service-account-issuer-discovery                                2025-06-24T14:06:03Z
system:volume-scheduler                                                2025-06-24T14:06:03Z
view                                                                   2025-06-24T14:06:03Z
controlplane:~$ 
______________________________________

Lab-1: View role - cluster level

controlplane:~$ 
controlplane:~$ kubectl  create  clusterrolebinding   read-only  --clusterrole=view  --user=jeff
clusterrolebinding.rbac.authorization.k8s.io/read-only created
controlplane:~$ 
controlplane:~$ kubectl  delete   clusterrolebinding  read-only 
clusterrolebinding.rbac.authorization.k8s.io "read-only" deleted
controlplane:~$ 
______________________________________

Lab-2: View role -- namespace level - default

controlplane:~$ 
controlplane:~$ kubectl create rolebinding read-access --clusterrole=view --user=jeff -n default
rolebinding.rbac.authorization.k8s.io/read-access created
controlplane:~$ 
controlplane:~$ kubectl  delete  rolebinding  read-access  -n  default

______________________________________
RBAC class-2

Lab-3: Cluster-admin (full-access)
	resources: '*'
	verbs: "*"


kubectl  create  clusterrolebinding  xyz  --clusterrole=cluster-admin  --group=staff
kubectl delete clusterrolebinding xyz
______
Jeff Node RUN :-

kubectl get nodes
kubectl get  pods


Lab-4: How to create and modify roles ?


controlplane:~$ 
controlplane:~$ kubectl get clusterrole cluster-admin -o yaml > role.yml
controlplane:~$ 
controlplane:~$ cat  role.yml 
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  annotations:
  name: pod-reader
rules:
- apiGroups:
  - 'v1'
  resources:
  - 'pods'
  verbs:
  - 'list'
controlplane:~$ 
[root@controlplane ~]#
[root@controlplane ~]# kubectl create -f role.yml
clusterrole.rbac.authorization.k8s.io/pod-reader created
[root@controlplane ~]#
[root@controlplane ~]# kubectl create clusterrole pod-reader --resource=pods  --verb=list     #### if create using same role using cmd
Error from server (AlreadyExists): clusterroles.rbac.authorization.k8s.io "pod-reader" already exists
[root@controlplane ~]#
[root@controlplane ~]# kubectl create clusterrolebinding pod-reader --clusterrole=pod-reader  --user=jeff
clusterrolebinding.rbac.authorization.k8s.io/pod-reader created
[root@controlplane ~]#
[root@controlplane ~]#

_____________________________________
jeff

kubectl get pod
kubectl get nodes
kubectl get ns
kubectl get pod -n default
kubectl delete pod test -n default
kubectl get pod -n kube-system
kubectl delete clusterrolebinding pod-reader
kubectl delete clusterrole pod-reader

kubectl create role pod-reader --resource=pods --verb=list -n default
kubectl create rolebinding pod-reader --role=pod-reader --user=jeff -n default
kubectl delete rolebinding pod-reader -n default
kubectl delete role pod-reader -n default

kubectl create clusterrole deployment-clusterrole  --resource=Deployment,DaemonSet,StatefulSet  --verb=create

__________________________________________________

Token based/Service Account Based authentication ?

kubectl create sa jenkins

kubectl create token jenkins


bastion node 

cp .kube/config  jenkins.conf

rm -rf .kube/config

vim jenkins.conf

root@k8s-master:~/certs# cat    jenkins.conf 
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJVGVMY3JRQWQ5d293RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBM01UWXhNelV6TXpKYUZ3MHpOVEEzTVRReE16VTRNekphTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURZNXIwUjhoV3Bsazl1MkJnZ3hyNHFESk5zNFNnb0pFdTFkbFZ5emRxMlEwaFIzcXM5aTk5RGNvWSsKRFNKK3RNakdyaE92UUMrU2dQQVViQW9QTlBIQ3hjTzU2T1d2K2V4dGlwdVcxSnZEVnNZK2hIenhBeFpvN293VQpFRm5CbVlVSnZaemNMT2FsUEd3V3FMaGI4RjNHTlJvYThKMjJrcmlOeUViRGZnQnFkcDZtalRaRnliK1k4UjhoCmx3MGUrUDI0cHpOTGg3R2lEa0VjcldpdzFocFNVK0dJTTFvd0ZySlBaeWp1V0NoVTdFWHhidWJ2OVF0MmVBa0UKQVhja3kxUHBkaGJHSUlFSUlqYlhOYXBrR1JQVkhlUHFXV0NieW1qOWlpcjRvK1RyS3RYcHZJNlNrc1FZMkswOQpTSWpYOFMvUmJucGxaUDZjSGsyU0FjSkFKUFdUQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJRWndrMnMzYklaNlNpZU5iT0FObUp1N1VyQmJ6QVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQUQydXZVeDZFMwpqaUNjNmJpT2xyeEMwM01MZTE2aTBvM1B4RWlEQk16WjlyQ1A2cEZpTWxBRGR3cVdrcGpVSDRJMVdjUFB3MlNuCjd0NlMwc01SOU9iS3Yrd21GQ2VUbDUrNlNaclBhaTF4UW5VYXFMNWhGVDAvSDh0QzRTYUhuYlI2b0xCdGdUQXkKeTMxWTljb1kxMUd5QW8rNGFWdzFXcmJRZ3ZJU1NxZk9vL1pQbEhHcHQvdktzQ2hmUFhhRlZXM3diQ1p2QVhkSQpFbkt5WWhJMGNjaWp6b3B3ZnZHZ2RUMGFncUtDTWhiNERVZXFOV2wyeEZadTR6YUV5SXp0bC9xUHVRUGx1Z1VECjJDNjhmazBjcUxRQXQwdHlURHdERkpvNWY3cDVtVS9mOExINUdpQWZscml1YTZxcWlrZksyS09xSFAxVThOdUkKS0R1T1p5RUJVU21aCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://172.31.64.216:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: jenkins
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: jenkins
  user:
    token: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJVGVMY3JRQWQ5d293RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBM01UWXhNelV6TXpKYUZ3MHpOVEEzTVRReE16VTRNekphTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURZNXIwUjhoV3Bsazl1MkJnZ3hyNHFESk5zNFNnb0pFdTFkbFZ5emRxMlEwaFIzcXM5aTk5RGNvWSsKRFNKK3RNakdyaE92UUMrU2dQQVViQW9QTlBIQ3hjTzU2T1d2K2V4dGlwdVcxSnZEVnNZK2hIenhBeFpvN293VQpFRm5CbVlVSnZaemNMT2FsUEd3V3FMaGI4RjNHTlJvYThKMjJrcmlOeUViRGZnQnFkcDZtalRaRnliK1k4UjhoCmx3MGUrUDI0cHpOTGg3R2lEa0VjcldpdzFocFNVK0dJTTFvd0ZySlBaeWp1V0NoVTdFWHhidWJ2OVF0MmVBa0UKQVhja3kxUHBkaGJHSUlFSUlqYlhOYXBrR1JQVkhlUHFXV0NieW1qOWlpcjRvK1RyS3RYcHZJNlNrc1FZMkswOQpTSWpYOFMvUmJucGxaUDZjSGsyU0FjSkFKUFdUQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJRWndrMnMzYklaNlNpZU5iT0FObUp1N1VyQmJ6QVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQUQydXZVeDZFMwpqaUNjNmJpT2xyeEMwM01MZTE2aTBvM1B4RWlEQk16WjlyQ1A2cEZpTWxBRGR3cVdrcGpVSDRJMVdjUFB3MlNuCjd0NlMwc01SOU9iS3Yrd21GQ2VUbDUrNlNaclBhaTF4UW5VYXFMNWhGVDAvSDh0QzRTYUhuYlI2b0xCdGdUQXkKeTMxWTljb1kxMUd5QW8rNGFWdzFXcmJRZ3ZJU1NxZk9vL1pQbEhHcHQvdktzQ2hmUFhhRlZXM3diQ1p2QVhkSQpFbkt5WWhJMGNjaWp6b3B3ZnZHZ2RUMGFncUtDTWhiNERVZXFOV2wyeEZadTR6YUV5SXp0bC9xUHVRUGx1Z1VECjJDNjhmazBjcUxRQXQwdHlURHdERkpvNWY3cDVtVS9mOExINUdpQWZscml1YTZxcWlrZksyS09xSFAxVThOdUkKS0R1T1p5RUJVU21aCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K

:wq!

kubectl  config  get-contexts  --kubeconfig=jenkins.conf

kubectl  get  pod  -A  --kubeconfig=jenkins.conf

kubectl  create  clusterrolebinding  full-access   --clusterrole=cluster-admin   --serviceaccount=default:jenkins  #### controlplane command

kubectl get pod   --kubeconfig=jenkins.conf

kubectl get nodes   --kubeconfig=jenkins.conf

kubectl delete sa jenkins  #### controlplane command

kubectl get nodes   --kubeconfig=jenkins.conf

kubectl delete clusterrolebinding full-access  #### controlplane command





Lab-1: How to provide service to administrator ?

Lab-2: How to use service account for the services?


kubectl create ns monitoring

kubectl create deploy cli-detail --image=nginx -n monitoring

kubectl get pod -n monitoring

kubectl cp /bin/kubectl cli-detail-986787:tmp

kubectl exec -it  cli-detail-98989  bash

/tmp/kubectl get pod -A

exit

kubectl create sa cli-sa -n monitoring

kubectl create clusterrolebinding view-access --clusterrole=view  --serviceaccount=monitoring:cli-sa

kubectl edit deploy cli-detail -n monitoring

serviceAccountName: cli-sa  ####  Add this line

:wq!


kubectl get  po -n monitoring 

kubectl cp /bin/kubectl  cli-details-9898:/tmp -n monitoring

chmod u+x /tmp/kubectl 

kubectl exec -it  cli-detail-8989 -- bash -n monitoring

/tmp/kubeclt  get pod -n kube-system

exit















