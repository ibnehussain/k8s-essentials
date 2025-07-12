# Kubernetes Services Lab: ClusterIP, NodePort, LoadBalancer

## 🔰 Use Case Setup: Simple NGINX App

### 🧩 Task 1: Create a Deployment
Create a Deployment for NGINX (which will be used by all service types).

#### `nginx-deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
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
        image: nginx:latest
        ports:
        - containerPort: 80
```

#### Commands:
```bash
# Create the deployment from YAML
kubectl apply -f nginx-deployment.yaml

# Verify that pods are created and running, with detailed node info
kubectl get pods -o wide
```

---

## 1️⃣ ClusterIP (Default)
> Internal communication only — accessible **within the cluster**.

### 🧩 Task 2: Create ClusterIP Service

#### `clusterip-service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-clusterip
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

#### Commands:
```bash
# Create the ClusterIP service from YAML
kubectl apply -f clusterip-service.yaml

# Verify service is created and note the ClusterIP address
kubectl get svc nginx-clusterip
```

### 🧩 Task 3: Test ClusterIP Service from inside the cluster
```bash
# Launch an interactive BusyBox pod to test internal access
kubectl run busybox --image=busybox:1.28 --restart=Never -it -- sh

# Inside pod shell: send HTTP request to the service
wget -qO- http://nginx-clusterip
```

---

## 2️⃣ NodePort
> Exposes service **on each Node’s IP at a static port**.

### 🧩 Task 4: Create NodePort Service

#### `nodeport-service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080  # Range: 30000–32767
  type: NodePort
```

#### Commands:
```bash
# Create the NodePort service
kubectl apply -f nodeport-service.yaml

# View the service and identify the exposed NodePort
kubectl get svc nginx-nodeport
```

### 🧩 Task 5: Test NodePort Service from browser or terminal
```bash
# Get the external/public IP of the worker nodes
kubectl get nodes -o wide

# Access the service via any node's IP and NodePort
curl http://<NodeIP>:30080
```

---

## 3️⃣ LoadBalancer
> Exposes service **externally using a cloud provider’s LoadBalancer**.

### 🧩 Task 6: Create LoadBalancer Service

#### `loadbalancer-service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-loadbalancer
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
```

#### Commands:
```bash
# Create the LoadBalancer service
kubectl apply -f loadbalancer-service.yaml

# Watch until the external IP is assigned by the cloud provider
kubectl get svc nginx-loadbalancer --watch
```

### 🧩 Task 7: Test LoadBalancer Service
```bash
# Once EXTERNAL-IP is available, access it via curl or browser
curl http://<External-IP>
```

---

## Cleanup

### 🧩 Task 8: Cleanup Resources
```bash
# Delete deployment and all services
kubectl delete -f nginx-deployment.yaml
kubectl delete -f clusterip-service.yaml
kubectl delete -f nodeport-service.yaml
kubectl delete -f loadbalancer-service.yaml

# Delete test pod used for ClusterIP validation
kubectl delete pod busybox
```

