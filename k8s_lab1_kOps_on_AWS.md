
# Kubernetes Essentials

---

## Lab 1: Kubernetes Operations (kops) on AWS
🌐 What is kOps?
kOps (Kubernetes Operations) is a command-line tool that helps you create, manage, upgrade, and destroy Kubernetes clusters easily, especially on cloud providers like AWS.
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