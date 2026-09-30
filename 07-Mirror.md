# Mirroring

This task demonstrates the traffic mirroring capabilities of Istio.

Traffic mirroring, also called shadowing, is a powerful concept that allows feature teams to bring changes to production with as little risk as possible. Mirroring sends a copy of live traffic to a mirrored service. The mirrored traffic happens out of band of the critical request path for the primary service.

In this task, you will first force all traffic to v1 of a test service. Then, you will apply a rule to mirror a portion of traffic to v2.

## Before you begin

1. Set up Istio by following the Installation guide.

2. Start by deploying two versions of the httpbin service that have access logging enabled:

- Deploy **httpbin-v1**:

kubectl create -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin-v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpbin
      version: v1
  template:
    metadata:
      labels:
        app: httpbin
        version: v1
    spec:
      containers:
      - image: docker.io/kennethreitz/httpbin
        imagePullPolicy: IfNotPresent
        name: httpbin
        command: ["gunicorn", "--access-logfile", "-", "-b", "[::]:80", "httpbin:app"]
        ports:
        - containerPort: 80

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin-v2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpbin
      version: v2
  template:
    metadata:
      labels:
        app: httpbin
        version: v2
    spec:
      containers:
      - image: docker.io/kennethreitz/httpbin
        imagePullPolicy: IfNotPresent
        name: httpbin
        command: ["gunicorn", "--access-logfile", "-", "-b", "[::]:80", "httpbin:app"]
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: httpbin
  labels:
    app: httpbin
spec:
  ports:
  - name: http
    port: 8000
    targetPort: 80
  selector:
    app: httpbin
EOF

## Creating a default routing policy

By default Kubernetes load balances across both versions of the httpbin service. In this step, you will change that behavior so that all traffic goes to v1.

1. Create a default route rule to route all traffic to v1 of the service:

kubectl create -f - <<EOF

apiVersion: networking.istio.io/v1
kind: VirtualService
metadata:
  name: httpbin
spec:
  hosts:
  - httpbin
  http:
  - route:
    - destination:
        host: httpbin
        subset: v1
      weight: 100
---
apiVersion: networking.istio.io/v1
kind: DestinationRule
metadata:
  name: httpbin
spec:
  host: httpbin
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
EOF

2. Now, with all traffic directed to httpbin:v1, send a request to the service:

 kubectl exec deploy/curl -c curl -- curl -sS http://httpbin:8000/headers
{
  "headers": {
    "Accept": "*/*",
    "Content-Length": "0",
    "Host": "httpbin:8000",
    "User-Agent": "curl/7.35.0",
    "X-B3-Parentspanid": "57784f8bff90ae0b",
    "X-B3-Sampled": "1",
    "X-B3-Spanid": "3289ae7257c3f159",
    "X-B3-Traceid": "b56eebd279a76f0b57784f8bff90ae0b",
    "X-Envoy-Attempt-Count": "1",
    "X-Forwarded-Client-Cert": "By=spiffe://cluster.local/ns/default/sa/default;Hash=20afebed6da091c850264cc751b8c9306abac02993f80bdb76282237422bd098;Subject=\"\";URI=spiffe://cluster.local/ns/default/sa/default"
  }
}


3. Check the logs from httpbin-v1 and httpbin-v2 pods. You should see access log entries for v1 and none for v2:

kubectl logs deploy/httpbin-v1 -c httpbin

127.0.0.1 - - [07/Mar/2018:19:02:43 +0000] "GET /headers HTTP/1.1" 200 321 "-" "curl/7.35.0"

 kubectl logs deploy/httpbin-v2 -c httpbin
 
<none>

# 
Documentation Tasks Traffic Management Mirroring
Mirroring
 6 minute read     page test

A template bug in the Istio website code means that the examples on this page do not render properly. You can view the page source to see the correct manifests.
This task demonstrates the traffic mirroring capabilities of Istio.

Traffic mirroring, also called shadowing, is a powerful concept that allows feature teams to bring changes to production with as little risk as possible. Mirroring sends a copy of live traffic to a mirrored service. The mirrored traffic happens out of band of the critical request path for the primary service.

In this task, you will first force all traffic to v1 of a test service. Then, you will apply a rule to mirror a portion of traffic to v2.

Istio supports the Kubernetes Gateway API and intends to make it the default API for traffic management in the future. The following instructions allow you to choose to use either the Gateway API or the Istio configuration API when configuring traffic management in the mesh. Follow instructions under either the Gateway API or Istio APIs tab, according to your preference.

Note that the Kubernetes Gateway API CRDs do not come installed by default on most Kubernetes clusters, so make sure they are installed before using the Gateway API:

$ kubectl get crd gateways.gateway.networking.k8s.io &> /dev/null || \
  kubectl apply --server-side -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.6.0/experimental-install.yaml

Before you begin
Set up Istio by following the Installation guide.

Start by deploying two versions of the httpbin service that have access logging enabled:

Deploy httpbin-v1:

$ kubectl create -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin-v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpbin
      version: v1
  template:
    metadata:
      labels:
        app: httpbin
        version: v1
    spec:
      containers:
      - image: docker.io/kennethreitz/httpbin
        imagePullPolicy: IfNotPresent
        name: httpbin
        command: ["gunicorn", "--access-logfile", "-", "-b", "[::]:80", "httpbin:app"]
        ports:
        - containerPort: 80
EOF

Deploy httpbin-v2:

 kubectl create -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin-v2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpbin
      version: v2
  template:
    metadata:
      labels:
        app: httpbin
        version: v2
    spec:
      containers:
      - image: docker.io/kennethreitz/httpbin
        imagePullPolicy: IfNotPresent
        name: httpbin
        command: ["gunicorn", "--access-logfile", "-", "-b", "[::]:80", "httpbin:app"]
        ports:
        - containerPort: 80
EOF

Deploy the httpbin Kubernetes service:

 kubectl create -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: httpbin
  labels:
    app: httpbin
spec:
  ports:
  - name: http
    port: 8000
    targetPort: 80
  selector:
    app: httpbin
EOF

Deploy the curl workload you’ll use to send requests to the httpbin service:

 cat <<EOF | kubectl create -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: curl
spec:
  replicas: 1
  selector:
    matchLabels:
      app: curl
  template:
    metadata:
      labels:
        app: curl
    spec:
      containers:
      - name: curl
        image: curlimages/curl
        command: ["/bin/sleep","3650d"]
        imagePullPolicy: IfNotPresent
EOF

Creating a default routing policy
By default Kubernetes load balances across both versions of the httpbin service. In this step, you will change that behavior so that all traffic goes to v1.

Create a default route rule to route all traffic to v1 of the service:


 kubectl apply -f - <<EOF
apiVersion: networking.istio.io/v1
kind: VirtualService
metadata:
  name: httpbin
spec:
  hosts:
- httpbin
  http:
  - route:
- destination:
    host: httpbin
    subset: v1
  weight: 100
---
apiVersion: networking.istio.io/v1
kind: DestinationRule
metadata:
  name: httpbin
spec:
  host: httpbin
  subsets:
  - name: v1
labels:
  version: v1
  - name: v2
labels:
  version: v2
EOF

Now, with all traffic directed to httpbin:v1, send a request to the service:

 kubectl exec deploy/curl -c curl -- curl -sS http://httpbin:8000/headers
{
  "headers": {
    "Accept": "*/*",
    "Content-Length": "0",
    "Host": "httpbin:8000",
    "User-Agent": "curl/7.35.0",
    "X-B3-Parentspanid": "57784f8bff90ae0b",
    "X-B3-Sampled": "1",
    "X-B3-Spanid": "3289ae7257c3f159",
    "X-B3-Traceid": "b56eebd279a76f0b57784f8bff90ae0b",
    "X-Envoy-Attempt-Count": "1",
    "X-Forwarded-Client-Cert": "By=spiffe://cluster.local/ns/default/sa/default;Hash=20afebed6da091c850264cc751b8c9306abac02993f80bdb76282237422bd098;Subject=\"\";URI=spiffe://cluster.local/ns/default/sa/default"
  }
}

Check the logs from httpbin-v1 and httpbin-v2 pods. You should see access log entries for v1 and none for v2:

$ kubectl logs deploy/httpbin-v1 -c httpbin
127.0.0.1 - - [07/Mar/2018:19:02:43 +0000] "GET /headers HTTP/1.1" 200 321 "-" "curl/7.35.0"

$ kubectl logs deploy/httpbin-v2 -c httpbin
<none>

# Mirroring traffic to httpbin-v2

1. Change the route rule to mirror traffic to **httpbin-v2**:

kubectl apply -f - <<EOF
apiVersion: networking.istio.io/v1
kind: VirtualService
metadata:
  name: httpbin
spec:
  hosts:
  - httpbin
  http:
  - route:
    - destination:
        host: httpbin
        subset: v1
      weight: 100
    mirror:
      host: httpbin
      subset: v2
    mirrorPercentage:
      value: 100.0
EOF

2. Send the traffic:

$ kubectl exec deploy/curl -c curl -- curl -sS http://httpbin:8000/headers

Now, you should see access logging for both v1 and v2. The access logs created in v2 are the mirrored requests that are actually going to v1.

$ kubectl logs deploy/httpbin-v1 -c httpbin

127.0.0.1 - - [07/Mar/2018:19:02:43 +0000] "GET /headers HTTP/1.1" 200 321 "-" "curl/7.35.0"
127.0.0.1 - - [07/Mar/2018:19:26:44 +0000] "GET /headers HTTP/1.1" 200 321 "-" "curl/7.35.0"

$ kubectl logs deploy/httpbin-v2 -c httpbin

127.0.0.1 - - [07/Mar/2018:19:26:44 +0000] "GET /headers HTTP/1.1" 200 361 "-" "curl/7.35.0"
127.0.0.1 - - [07/Mar/2018:19:26:44 +0000] "GET /headers HTTP/1.1" 200 361 "-" "curl/7.35.0"

## Cleaning up

1. Remove the rules:
```
$ kubectl delete virtualservice httpbin
$ kubectl delete destinationrule httpbin
```
2. Delete httpbin and curl deployments and httpbin service:

```
kubectl delete deploy httpbin-v1 httpbin-v2 curl
kubectl delete svc httpbin

```



















