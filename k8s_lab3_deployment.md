# Lab 3: Kubernetes Deployment

---

## 🧪 Task 1: Write a Deployment YAML and Apply It

### 📄 Create a Deployment YAML
```bash
vi dep-nginx.yaml
```
#### ✏️ YAML Content:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-dep
  labels:
    app: nginx-dep
spec:
  replicas: 3
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: nginx-app
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx-ctr
        image: nginx:1.11
        ports:
        - containerPort: 80
```

### 🛠 Apply the Deployment
```bash
kubectl apply -f dep-nginx.yaml
# ➤ Creates the Deployment and underlying ReplicaSet with 3 nginx pods
```

### 🔍 View Created Objects
```bash
kubectl get deployments
# ➤ Lists all Deployments, including nginx-dep

kubectl get rs
# ➤ Shows the ReplicaSet automatically created and managed by the Deployment
```

### 🔎 Access Pod to Check nginx Version
```bash
kubectl get pods
# ➤ Lists all running pods (copy one pod name for next step)

kubectl exec -it <pod_name> -- /bin/bash
# ➤ Opens a shell into the container inside the pod

# nginx -v
# ➤ Displays the installed nginx version
# exit
```

---

## 🔄 Task 2: Update the Deployment with a Newer Image

```bash
kubectl set image deployment/nginx-dep nginx-ctr=nginx:1.12.2
# ➤ Updates the image used by the Deployment to nginx:1.12.2
```

### 🔍 Confirm Update
```bash
kubectl describe deployments
# ➤ Shows details of Deployment, including rollout status and container images

kubectl get pods
# ➤ Observe new pods with the updated image being created

kubectl exec -it <pod_name> -- /bin/bash
# ➤ Access the pod shell again to verify the version

# nginx -v
# exit
```

---

## ⏪ Task 3: Rollback the Deployment

```bash
kubectl rollout history deployment/nginx-dep
# ➤ Shows previous versions (revisions) of the Deployment

kubectl rollout undo deployment/nginx-dep --to-revision=1
# ➤ Rolls back to a previous image version (in this case, nginx:1.11)
```

### 🔎 Verify Rollback
```bash
kubectl get rs
# ➤ See the new ReplicaSet created for rollback

kubectl get pods
kubectl exec -it <pod_name> -- /bin/bash

# nginx -v
# exit
```

---

## 📈 Task 4: Scaling the Deployment

### 🔍 Check Current Replica Count
```bash
kubectl get deployments
kubectl get pods
# ➤ View current replica count and running pods
```

### 🔼 Scale Up to 8 Replicas
```bash
kubectl scale deployment nginx-dep --replicas=8
# ➤ Increases the number of nginx pods to 8
```

### 🔽 Scale Down to 2 Replicas
```bash
kubectl scale deployment nginx-dep --replicas=2
# ➤ Reduces the number of nginx pods to 2
```

### 🔍 Verify Scaling
```bash
kubectl get deployments
kubectl get pods
# ➤ Confirm scaling changes are applied
```

---

## 🧹 Task 5: Cleanup

```bash
kubectl delete -f dep-nginx.yaml
# ➤ Deletes the Deployment and associated ReplicaSets and Pods
```
