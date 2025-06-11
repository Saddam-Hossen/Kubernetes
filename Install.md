# Kubernetes
Here's a complete **step-by-step documentation** for installing **Kubernetes (Minikube + kubectl)** on **Ubuntu**.

---

## 🧾 Kubernetes Setup on Ubuntu (Minikube + kubectl + Docker)

---

### ✅ Prerequisites

1. **Ubuntu OS**
2. **Internet Connection**
3. **Terminal with `sudo` privileges**

---

### 🧱 Step 1: Install Docker

Docker is required for Minikube to run containers.

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

> ⚠️ You might need to restart your terminal or log out/in after adding yourself to the `docker` group.

---

### 📦 Step 2: Install Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Verify:

```bash
minikube version
```

---

### 🔧 Step 3: Install kubectl (Kubernetes CLI)

```bash
sudo snap install kubectl --classic
```

Verify:

```bash
kubectl version --client
```

---

### 🚀 Step 4: Start Minikube

Start your Minikube cluster using Docker as the driver:

```bash
minikube start --driver=docker
```

---

### 🔍 Step 5: Verify Cluster

Check node status:

```bash
kubectl get nodes
```

Expected output:

```
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   1m    v1.33.x
```

---

### 🧪 Optional: Enable Dashboard

To use the Kubernetes dashboard (web UI):

```bash
minikube dashboard
```

---

## ✅ Done!

You now have:

* A local Kubernetes cluster (`minikube`)
* Kubernetes CLI (`kubectl`)
* Docker container runtime

---

### 📁 Suggested Directory Structure for Projects

```
my-microservice-app/
├── Dockerfile
├── deployment/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── autoscaler.yaml
├── src/
├── pom.xml
```

---


* Try `kubectl apply -f` for deploying via YAML
* Explore Helm charts
* Use `minikube dashboard` for a visual interface

---

Would you like a sample `YAML` file to try deploying, or a quick walkthrough of Kubernetes concepts using this Minikube setup?
