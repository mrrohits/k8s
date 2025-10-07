### Notes on **NodeSelector** in Kubernetes

---

### **1. NodeSelector Overview**

**NodeSelector** is a simple mechanism that allows Pods to be scheduled only on nodes that have specific labels. It's the basic form of **node affinity** and works by matching node labels with selectors defined in the Pod spec.

#### **Example: Simple NodeSelector in a Pod**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  nodeSelector:
    disktype: ssd  # Only schedule Pod on nodes with label disktype=ssd
  containers:
  - name: nginx
    image: nginx
```

* **Explanation**: The Pod will only be scheduled on nodes that have the label `disktype=ssd`.

---

### **2. NodeSelector at Deployment Level**

You can also specify **NodeSelector** at the **Deployment** level to constrain Pods created by the Deployment to nodes with specific labels.

#### **Example: NodeSelector in Deployment**

1. **Label the Node**:

   ```bash
   kubectl label node node01 disk=ssd
   ```

2. **Deployment YAML with NodeSelector**:

   ```yaml
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
         nodeSelector:    # NodeSelector at deployment level
           disk: ssd       # Pods will only be scheduled on nodes with `disk=ssd`
         containers:
           - name: nginx
             image: docker.io/nginx
   ```

* **Explanation**:

  * This Deployment will create Pods that only run on nodes labeled `disk=ssd`.

---

### **3. NodeSelector at Namespace Level**

NodeSelector can also be set at the **Namespace** level using annotations. This ensures that all Pods created in that namespace will follow the **nodeSelector** constraint.

#### **Steps to Set NodeSelector at Namespace Level**:

1. **Create a Namespace**:

   ```bash
   kubectl create ns ygminds
   ```

2. **Edit Namespace to Add NodeSelector Annotation**:

   ```bash
   kubectl edit ns ygminds
   ```

   * **Edit the YAML**:

     ```yaml
     apiVersion: v1
     kind: Namespace
     metadata:
       annotations:
         scheduler.alpha.kubernetes.io/node-selector: disk=ssd  # Add nodeSelector here
       name: ygminds
     ```

3. **Label the Node**:

   ```bash
   kubectl label node node01 disk=ssd
   ```

* **Explanation**:

  * This configuration ensures that all Pods created within the `ygminds` namespace will be scheduled only on nodes labeled `disk=ssd`.

---

### **4. Enabling Namespace-Level NodeSelector in Kubernetes API Server**

To use **NodeSelector at the namespace level**, you need to enable the `PodNodeSelector` feature in the Kubernetes API server.

* **Steps**:

  * Edit the Kubernetes API server manifest file (`/etc/kubernetes/manifests/kube-apiserver.yaml`).
  * Add the `PodNodeSelector` parameter to `--enable-admission-plugins`.

* **Reference**: [StackOverflow Link](https://stackoverflow.com/questions/52487333/how-to-assign-a-namespace-to-certain-nodes)

---

### **5. Deploy a Pod in a Namespace with NodeSelector**

1. **Create a Deployment in the `ygminds` Namespace**:

   ```bash
   kubectl create deploy test --image=docker.io/nginx -n ygminds
   ```

2. **Check Pods in the Namespace**:

   ```bash
   kubectl get pod -n ygminds
   ```

* **Explanation**: The deployment will create Pods that adhere to the NodeSelector in the `ygminds` namespace, ensuring they run only on nodes with `disk=ssd`.

---

### **Key Concepts & Use Cases**

* **NodeSelector**: Simple method to constrain Pods to nodes with specific labels (e.g., `disktype=ssd`).
* **Deployment-Level NodeSelector**: Use NodeSelector to control which nodes a deployment’s Pods are scheduled on.
* **Namespace-Level NodeSelector**: Control scheduling for all Pods within a specific namespace by adding annotations.
* **Feature Flag**: Enabling `PodNodeSelector` at the API server level for namespace-based scheduling.

---

### **Summary of Commands and Key Concepts**:

1. **Label Nodes**:

   ```bash
   kubectl label node node01 disk=ssd
   ```

2. **Create a Namespace**:

   ```bash
   kubectl create ns ygminds
   ```

3. **Set NodeSelector in Namespace**:

   ```bash
   kubectl edit ns ygminds
   ```

4. **Deploy Pod in Namespace**:

   ```bash
   kubectl create deploy test --image=docker.io/nginx -n ygminds
   ```

5. **Check Pods in Namespace**:

   ```bash
   kubectl get pod -n ygminds
   ```

By using **NodeSelector** at different levels (Pod, Deployment, Namespace), you can fine-tune where your Pods are scheduled and optimize resource allocation in your Kubernetes cluster.
