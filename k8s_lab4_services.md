# Kubernetes Services Lab: ClusterIP, NodePort, LoadBalancer

## 🔰 Use Case Setup: Simple NGINX App

Create a Deployment for NGINX (used by all service types).

### `nginx-deployment.yaml`
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

### Commands:
```bash
kubectl apply -f nginx-deployment.yaml
kubectl get pods -o wide
```

---

## 1️⃣ ClusterIP (Default)
> Internal communication only — accessible **within the cluster**.

### `clusterip-service.yaml`
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

### Commands:
```bash
kubectl apply -f clusterip-service.yaml
kubectl get svc nginx-clusterip
```

### Test (From inside a Pod):
```bash
kubectl run busybox --image=busybox:1.28 --restart=Never -it -- sh
# Inside pod shell:
wget -qO- http://nginx-clusterip
```

---

## 2️⃣ NodePort
> Exposes service **on each Node’s IP at a static port**.

### `nodeport-service.yaml`
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

### Commands:
```bash
kubectl apply -f nodeport-service.yaml
kubectl get svc nginx-nodeport
```

### Test:
```bash
# Get Node External IP
kubectl get nodes -o wide

# Access service:
curl http://<NodeIP>:30080
```

---

## 3️⃣ LoadBalancer
> Exposes service **externally using a cloud provider’s LoadBalancer**.

### `loadbalancer-service.yaml`
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

### Commands:
```bash
kubectl apply -f loadbalancer-service.yaml
kubectl get svc nginx-loadbalancer --watch
```

### Test:
```bash
curl http://<External-IP>
```

---


## Cleanup 
```bash
kubectl delete -f nginx-deployment.yaml
kubectl delete -f clusterip-service.yaml
kubectl delete -f nodeport-service.yaml
kubectl delete -f loadbalancer-service.yaml
kubectl delete pod busybox
```

