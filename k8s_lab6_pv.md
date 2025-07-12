# 🧪 Lab: Using PersistentVolume (PV) in Kubernetes

---

## 🧩 Step-by-Step Implementation
## 🧰 Task 1: Check Your Cluster

```bash
kubectl get nodes
````

✅ Ensure at least one node is in `Ready` state.

---

## 🛠️ Task 2: Create a Static PersistentVolume (PV)

### 📝 Step 1: Create `pv.yaml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data
```

### Apply the PV

```bash
kubectl apply -f pv.yaml
```

---

## 📥 Task 3: Create a PersistentVolumeClaim (PVC)

### 📝 Step 2: Create `pvc.yaml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

### Apply the PVC

```bash
kubectl apply -f pvc.yaml
```

Check status:

```bash
kubectl get pvc
```

Look for `STATUS: Bound`.

---

## 🚀 Task 4: Create a Pod that Uses the PVC

### 📝 Step 3: Create `pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: myapp
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: myvolume
  volumes:
  - name: myvolume
    persistentVolumeClaim:
      claimName: my-pvc
```

### Apply the Pod

```bash
kubectl apply -f pod.yaml
kubectl get pod mypod -o wide
```

---

## 🧪 Task 5: Test Storage

### Step 4: Write to the Volume

```bash
kubectl exec -it mypod -- sh
echo "Hello from static PV!" > /usr/share/nginx/html/index.html
exit
```

### Step 5: Verify PV by creating Pod again
```bash
kubectl apply -f pod.yaml
kubectl get pod mypod -o wide


---

### 🧹 Task 6: Clean Up

```bash
kubectl delete -f pod.yaml
kubectl delete -f pvc.yaml
kubectl delete -f pv.yaml
```

```
## 🚮 Delete the Cluster and your EC2 server (kops)

To clean up your Kubernetes setup and avoid unwanted charges, follow these steps to delete your **Kubernetes cluster** and **EC2 resources**.

---

### 🔴 Step 1: Delete the Kubernetes Cluster

```bash
kops get cluster
kops delete cluster --name <your-cluster-name> --yes
````

---

### 🔴 Step 2: Terminate the EC2 Instance (if manually created)

1. Go to **AWS Console > EC2 > Instances**
2. Select your instance(s)
3. Click on **Actions > Instance State > Terminate**

✅ **Note:** This ensures you're not billed further.

---
### 🔴 Step 3: Delete S3 bucket

1. Go to **AWS Console > S3 **
2. Select your S3 bucket
3. Click on **Delete **
4. Enter the name of the bucket to confirm deletion and click on Delete bucket

### ✅ Verify Cleanup

* Confirm S3 bucket is gone:

* Check EC2 dashboard:
  No instances should be in “running” or “pending” state.

---

### 🧹 Done!

You’ve successfully removed the Kubernetes cluster and freed up AWS resources. 🎉
🎉  Well done completing these labs. Keep practicing. Happy Learning! 🚀
```


```
