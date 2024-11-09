
# Kubernetes YAML Configuration Files

This repository contains various Kubernetes YAML configuration files that define different resources for deploying and managing applications in a Kubernetes cluster. Below is an explanation of each file, its purpose, and instructions on how to use them.

## Overview of Files

### 1. `lab1.yml`
Defines a simple Pod running an Nginx container.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
    - name: nginx
      image: nginx
```
**Purpose:** This file defines a Pod named `redis` that runs a single container using the nginx image.

### 2. `lab.yml`
Creates a namespace and sets a resource quota for pods.
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nti
---
apiVersion: v1
kind: ResourceQuota
metadata:
  name: pods-quota
  namespace: nti
spec:
  hard:
    pods: "2"
```
**Purpose:** This file creates a namespace `nti` and sets a resource quota limiting the number of pods to 2 within that namespace.

### 3. `initC.yml`
Defines a Pod with an init container that runs before the main Redis container.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis-pod
spec:
  initContainers:
    - name: init-busybox
      image: busybox
  containers:
    - name: redis
      image: redis
```
**Purpose:** This file defines a Pod named `redis-pod` that includes an init container (`init-busybox`). The main container runs a Redis instance.

### 4. `ds.yml`
Creates a DaemonSet for running Fluentd as a log collector in the kube-system namespace.
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: elasticsearch
  namespace: kube-system
spec:
  selector:
    matchLabels:
      name: elasticsearch
  template:
    metadata:
      labels:
        name: elasticsearch
    spec:
      containers:
        - name: fluentd
          image: k8s.gcr.io/fluentd-elasticsearch:1.20
```
**Purpose:** This file creates a DaemonSet named `elasticsearch` in the kube-system namespace, running a fluentd container for log collection.

### 5. `deployment2.yml`
Defines a Deployment for managing an Nginx application with 3 replicas.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-app
      type: frontend
  template:
    metadata:
      labels:
        app: nginx-app
        type: frontend
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
```
**Purpose:** This file defines a Deployment named `nginx-deployment` with 3 replicas, each running an Nginx container.

### 6. `dbpod.yml`
Creates a Pod running a MySQL container, retrieving environment variables from a secret.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dbase
spec:
  containers:
    - name: mysql
      image: mysql:5.7
      envFrom:
        - secretRef:
            name: db-secret
```
**Purpose:** This file creates a Pod named `dbase`, running a MySQL container that retrieves environment variables from a secret called `db-secret`.

### 7. `db.yml`
A simple Pod definition for a MySQL container.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: db-pod
spec:
  containers:
    - name: mysql
      image: mysql:5.7
```
**Purpose:** This file defines a Pod named `db-pod` that runs a MySQL container.

### 8. `Cm.yml`
Defines a ConfigMap for application configuration.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: webapp-config-map
data:
  APP_COLOR: darkblue
```
**Purpose:** This file creates a ConfigMap named `webapp-config-map` with an application configuration key `APP_COLOR`.

### 9. `be.yml`
Creates a Service to expose a backend application.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    tier: backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
**Purpose:** This file defines a Service named `backend-service`, which exposes TCP port 80 to route traffic to the corresponding pods.

### 10. `a.yml`
Another Pod definition for Redis with an init container.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis-pod
spec:
  initContainers:
    - name: init-busybox
      image: busybox
  containers:
    - name: redis
      image: redis
```
**Purpose:** Similar to `initC.yml`, this file creates another `redis-pod` with an init container and a main Redis container.

## How to Use These Files

### Prerequisites
- **Kubernetes Cluster**: Ensure you have access to a running Kubernetes cluster. You can use Minikube, kind, or a managed Kubernetes service.
- **kubectl**: Install the Kubernetes command-line tool, `kubectl`, and configure it to communicate with your cluster.

### Steps to Deploy

1. **Clone the Repository**
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. **Create the Namespace and Resource Quota**
   ```bash
   kubectl apply -f lab.yml
   ```

3. **Deploy the Pods and Other Resources**
   You can apply each of the YAML files using the following command:
   ```bash
   kubectl apply -f <file-name>.yml
   ```
   For example:
   ```bash
   kubectl apply -f lab1.yml
   kubectl apply -f initC.yml
   kubectl apply -f ds.yml
   kubectl apply -f deployment2.yml
   kubectl apply -f dbpod.yml
   kubectl apply -f db.yml
   kubectl apply -f Cm.yml
   kubectl apply -f be.yml
   kubectl apply -f a.yml
   ```

4. **Verify the Deployments**
   Use the following commands to check the status of your resources:
   ```bash
   kubectl get pods -n nti
   kubectl get services
   kubectl get deployments
   kubectl get daemonsets -n kube-system
   ```

### Accessing Services
If you have created a Service, you can access it according to its type (e.g., NodePort, LoadBalancer). For example:
```bash
kubectl get svc backend-service
```
Note the NodePort and access it using the Node's IP address.

### Cleanup
To delete all resources created by these files, you can run:
```bash
kubectl delete namespace nti
```

## Conclusion
This README provides an overview of the Kubernetes YAML files in this repository, including their purposes and instructions for usage. If you have any questions or need further assistance, feel free to reach out!
