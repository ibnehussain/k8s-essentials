
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
# Kubernetes Commands with Explanations

```bash
k create -f mypod.yaml
# ➤ Creates a pod (or any K8s resource) from the configuration file `mypod.yaml`

kubectl get pod
# ➤ Lists all pods in the current namespace with basic status info

alias k=kubectl
# ➤ Sets a shortcut so you can use `k` instead of typing `kubectl` every time

k get pod
# ➤ Same as `kubectl get pod` — shows all pods in the current namespace

k get pod -o wide
# ➤ Shows pods with extra details like node name, IP address, etc.

k get nodes
# ➤ Lists all nodes in the cluster with their status, roles, and version

k describe pod nginx-pod
# ➤ Displays detailed information about the pod named `nginx-pod` (events, containers, IPs, etc.)

kubectl api-resources
# ➤ Lists all Kubernetes resource types (e.g., pods, services, deployments) supported by the cluster

k get po -n kube-system
# ➤ Lists all pods in the `kube-system` namespace (K8s system components)

k delete pod nginx-pod
# ➤ Deletes the pod named `nginx-pod` from the cluster

k delete pod nginx-pod
# ➤ (Duplicate) Again deletes `nginx-pod`; has no effect if it's already deleted

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

```
# Kubernetes ReplicaSet: Apply and Manage

This guide walks through basic `kubectl` commands to apply and manage a ReplicaSet (`nginx-rs.yaml`) in Kubernetes.

---

## Step 1: Create a ReplicaSet

```bash
kubectl create -f nginx-rs.yaml
```
- ✅ This command creates a ReplicaSet using the YAML file `nginx-rs.yaml`.
- The ReplicaSet will ensure a specified number of nginx pods are running.

---

## Step 2: View the Running Pods

```bash
kubectl get pods
```
- ✅ Lists all pods in the current namespace.
- You should see the pods created by the ReplicaSet.

---

## Step 3: Filter Pods by Label

```bash
kubectl get pod -l app=nginx
```
- ✅ Filters and shows only pods with the label `app=nginx`.
- Useful to isolate pods created by a particular ReplicaSet or deployment.

---

## Step 4: Inspect the ReplicaSet

```bash
kubectl get rs nginx-rs -o wide
```
- ✅ Shows detailed information about the ReplicaSet named `nginx-rs`.
- The `-o wide` flag adds additional columns like node info and IPs.

---

## Step 5: Describe the ReplicaSet

```bash
kubectl describe rs nginx-rs
```
- ✅ Displays in-depth information: selector, pod template, events, and current status.
- Helps troubleshoot scheduling issues or verify configuration.

---

## Step 6: Confirm Pods Are Running

```bash
kubectl get pod
```
- ✅ Re-check the pods after creation or deletion to confirm the desired state.

---

## Step 7: Delete a Pod (for demo)

```bash
kubectl delete pod nginx-rs-764mq
```
- ⚠️ Deletes a pod **manually**. Replace the name with an actual pod from your list.
- Since the ReplicaSet manages the pods, Kubernetes will **automatically recreate** it to maintain the replica count.

---

## Step 8: Verify Pod Re-creation

```bash
kubectl get pod
```
- ✅ Observe that a **new pod with a different name** is created, showing ReplicaSet self-healing behavior.

---

## Step 9: View ReplicaSet Again

```bash
kubectl get rs
```
- ✅ Checks current ReplicaSets. Should still show `nginx-rs` with the same replica count.

---

## Step 10: Delete the ReplicaSet

```bash
kubectl delete rs nginx-rs
```
- ✅ Deletes the ReplicaSet named `nginx-rs`.
- All pods it manages are **deleted automatically** unless specified otherwise.

---

## Step 11: Confirm Deletion

```bash
kubectl get rs
kubectl get po
```
- ✅ Verifies that the ReplicaSet and its pods are no longer present.

---
