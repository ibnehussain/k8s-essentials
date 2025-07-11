
# Kubernetes Essentials

---

## Lab 1: Kubernetes Operations (kops) on AWS

### Task 1: Launch an EC2 Instance
- Create an EC2 Instance: Ubuntu Server 22.04 LTS (HVM), SSD Volume type `t2.micro`

### Task 2: Create an IAM Role
- Create an IAM role named `kops-admin-role` with the `AdministratorAccess` policy attached.
- Associate the IAM role with your EC2 instance `kops`:
  1. Go to EC2 Console and select the `kops` instance.
  2. Click on **Actions** > **Security** > **Modify IAM Role**.
  3. Search for `kops-admin-role`, select it, and click **Update IAM Role**.

### Task 3: Setting up a Kubernetes Cluster
```bash
sudo hostnamectl set-hostname kops
bash
```
- Create a file:
```bash
vi kops.sh
```
- Copy the content from the reference kops.sh file.
- Save and exit:
```bash
:wq
chmod 400 kops.sh
. ./kops.sh
```
- Validate the cluster:
```bash
kops get cluster
kops validate cluster <cluster-name>
kubectl get nodes
kops export kubeconfig --admin
```
- (Optional) Set KOPS state store:
```bash
export KOPS_STATE_STORE=s3://<your-bucket>
```

### Delete the Cluster:
```bash
kops delete cluster --name=example.k8s.local --yes
```

---

## Lab 2: Creating Pods

### Task 1: Imperative Method
```bash
kubectl run test --image=nginx:latest --port 80
kubectl get pod
alias k=kubectl
k get pod
k get pod -o wide
k get nodes
k describe pod test
```

### Task 2: Declarative Method
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
Commands:
```bash
kubectl create -f mypod.yaml
kubectl get pod
k get pod -o wide
k describe pod nginx-pod
kubectl api-resources
k get po -n kube-system
k delete pod nginx-pod
```

### Task 3: ReplicaSet
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
Commands:
```bash
kubectl create -f nginx-rs.yaml
kubectl get pods
kubectl get pod -l app=nginx
kubectl get rs nginx-rs -o wide
kubectl describe rs nginx-rs
kubectl delete pod <one-nginx-pod-name>
kubectl delete rs nginx-rs
```
