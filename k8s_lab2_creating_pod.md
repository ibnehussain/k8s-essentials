
# LAB-2: Creating Pod

## Task 1: Imperative Method

```bash
kubectl run test --image=nginx:latest --port 80

kubectl get pod
alias k=kubectl
k get pod
k get pod -o wide
k get nodes
k describe pod test
```

---

## Task 2: Declarative Method

Create file:
```bash
vi mypod.yaml
```

Content:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
```

Apply and verify:
```bash
k create -f mypod.yaml
kubectl get pod
alias k=kubectl
k get pod
k get pod -o wide
k get nodes
k describe pod nginx-pod

kubectl api-resources
k get po -n kube-system
k delete pod nginx-pod
k delete pod nginx-pod
```

---

## Task 3: ReplicaSet

Create file:
```bash
vi nginx-rs.yaml
```

Content:
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
  labels:
    app: nginx
spec:
  replicas: 3
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
        image: nginx
        ports:
        - containerPort: 80
```

Apply and manage:
```bash
kubectl create -f nginx-rs.yaml
kubectl get pods
kubectl get pod -l app=nginx
kubectl get rs nginx-rs -o wide
kubectl describe rs nginx-rs
kubectl get pod
kubectl delete pod nginx-rs-764mq
kubectl get pod
kubectl get rs
kubectl delete rs nginx-rs
kubectl get rs
kubectl get po
```
