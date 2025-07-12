
# DaemonSet - Kubernetes Lab

---

## 🧰 Task 1: Check Cluster Setup

### ✅ Prerequisites

Ensure your Kubernetes cluster is running and `kubectl` is configured.

```bash
# Ensure nodes are ready
kubectl get nodes

# Check kubectl context
kubectl config current-context
```

---

## 🛠️ Task 2: Create DaemonSet YAML

### 📝 Step 1: Create the YAML File

```bash
nano daemonset.yaml
```

Paste the following content:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: log-agent
spec:
  selector:
    matchLabels:
      name: log-agent
  template:
    metadata:
      labels:
        name: log-agent
    spec:
      containers:
      - name: log-collector
        image: busybox
        command: ["/bin/sh", "-c", "while true; do echo logs; sleep 30; done"]
```

---

## 🚀 Task 3: Deploy the DaemonSet

### 🏗️ Step 2: Apply the YAML

```bash
kubectl apply -f daemonset.yaml
```

✔️ This will:
- Create a pod **on every node**
- Each pod will continuously log a message every 30 seconds

---

## 🔎 Task 4: Verify Deployment

### 📋 Step 3: View DaemonSet and Pods

```bash
# List DaemonSets
kubectl get daemonsets

# List pods across nodes
kubectl get pods -o wide

# Detailed info
kubectl describe ds log-agent
```

> ✅ You should see one pod per node running the `log-agent`.

---

## 🔄 Task 5: Add a New Node (Test DaemonSet Behavior)

We’ll now simulate adding a new node to verify DaemonSet automatically places a pod on it.

---

## ⚙️ Task 6: Edit AWS Auto Scaling Group (Console Method)

### 🔐 Step 1: Login to AWS Console

- Visit [https://console.aws.amazon.com/ec2](https://console.aws.amazon.com/ec2)

---

### 📁 Step 2: Go to Auto Scaling Groups

- In the left sidebar, select **Auto Scaling Groups**

---

### 🔍 Step 3: Locate Your Node Group

- Search for your ASG name:
  - `nodes.cluster-name.k8s.local` *(Kops)*
  - or `eks-nodegroup-<name>` *(EKS)*

- Click on the ASG name to open its details

---

### 🧾 Step 4: Edit Desired Capacity

- Click **Edit**
- Update the following fields:
  - **Desired Capacity** → e.g., from `2` to `3`
  - *(Optional)* **Min Size**
  - *(Optional)* **Max Size**

- Click **Update**

> 💡 AWS will now launch a new EC2 instance.

---

## ✅ Task 7: Verify in Kubernetes

Wait 2–4 minutes for the new node to be registered.

### 🖥️ Step 1: Confirm Node is Ready

```bash
kubectl get nodes
```

You should see the new node in `Ready` state.

---

### 🔍 Step 2: Check DaemonSet Pod on New Node

```bash
kubectl get pods -o wide
```

A new pod of `log-agent` should now appear on the new node.

---

### 📊 Step 3: Confirm in DaemonSet

```bash
kubectl get ds
```

You’ll see an updated count in the `DESIRED` and `CURRENT` columns.

---

## 🧹 Task 8: Clean Up

```bash
kubectl delete -f daemonset.yaml
```

✔️ This will remove the DaemonSet and its pods from all nodes.

---
