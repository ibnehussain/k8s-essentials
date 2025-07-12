🔎 **Check Cluster Setup**:

```bash
# Ensure nodes are ready
kubectl get nodes

# Check kubectl context
kubectl config current-context
````

---

## 📄 Step-by-Step Deployment

### 1️⃣ Create DaemonSet YAML file

```bash
nano daemonset.yaml
```

Paste the following YAML:

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

### 2️⃣ Apply DaemonSet

```bash
kubectl apply -f daemonset.yaml
```

✔️ **What Happens?**

* One pod is scheduled **per node**
* Each pod runs a simple log loop

---

### 3️⃣ Verify Deployment

```bash
# List DaemonSets
kubectl get daemonsets

# List pods and see node distribution
kubectl get pods -o wide

# Describe the DaemonSet
kubectl describe ds log-agent
```

> You should see **one pod per node**, with each pod running on a different node.

---

---

### 4️⃣ Add a New Node (Advanced Test)

If you're using a dynamic cluster (like EKS, GKE, AKS, or a local multi-node setup), try adding a node.

✅ A new pod will automatically be scheduled on that new node!

## 🧭 Steps to Edit Auto Scaling Group via AWS Console

### Login to AWS Console

- Go to EC2 Console

---

### Navigate to Auto Scaling Groups

- In the left sidebar, scroll to **Auto Scaling** → click on **Auto Scaling Groups**

---

### Locate Your Node Group ASG

- Look for an ASG with a name like:
  - `nodes.cluster-name.k8s.local` *(Kops)*
  - or something like `eks-nodegroup-<name>` *(EKS)*

- Click on the **ASG name** to open its details.

---

### Edit Desired Capacity

- In the ASG dashboard, click **Edit** (usually in the top right corner).
- Modify the following:
  - **Desired Capacity**: Increase (e.g., from 2 → 3)
  - *(Optional)* **Minimum Size**: Match or set accordingly
  - *(Optional)* **Maximum Size**: Ensure it's >= desired

- Click **Update**

---

### Wait for the Node to Launch

- A new EC2 instance will be launched automatically.
- It will join your Kubernetes cluster.

---

## 🔍 Verify in Kubernetes

Once the new node joins (may take 2–4 minutes):

```bash
kubectl get nodes
---

### Clean Up

```bash
kubectl delete -f daemonset.yaml
```

✔️ All DaemonSet-managed pods will be removed.

---

