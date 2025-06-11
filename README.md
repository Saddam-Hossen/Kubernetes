# Kubernetes
Great! Here’s a **beginner-friendly guide to getting started with Kubernetes on Windows** using **Minikube**, a tool that lets you run Kubernetes locally.

---

## ✅ Step-by-Step Guide: Set Up Kubernetes on Windows

### 🔧 Prerequisites

1. **Windows 10/11 (64-bit)** – preferably with **WSL2** enabled.
2. **Install the following tools**:

   * [Docker Desktop](https://www.docker.com/products/docker-desktop/)
   * [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)
   * [Minikube](https://minikube.sigs.k8s.io/docs/start/)

---

## 🪛 Step 1: Install Docker Desktop

1. Download Docker from [here](https://www.docker.com/products/docker-desktop/).
2. Install and run it.
3. Enable **WSL2 backend** during installation if prompted.
4. Verify it's running: Right-click Docker icon in system tray → Settings → General → "Use the WSL 2 based engine" should be checked.

---

## 📦 Step 2: Install `kubectl`

1. Open PowerShell as Administrator.
2. Run:

```powershell
choco install kubernetes-cli
```

> If you don’t have Chocolatey, install it first from [https://chocolatey.org/install](https://chocolatey.org/install).

3. Confirm installation:

```bash
kubectl version --client
```

---

## 🚀 Step 3: Install Minikube

1. Run this in PowerShell:

```powershell
choco install minikube
```

2. After install, verify:

```bash
minikube version
```

---

## 🔄 Step 4: Start Your Local Kubernetes Cluster

1. Start Minikube with Docker driver:

```bash
minikube start --driver=docker
```

> If you see WSL2-related issues, try:

```bash
minikube config set driver docker
```

2. Check status:

```bash
minikube status
```

---

## 🔍 Step 5: Test the Cluster

Create a sample app:

```bash
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

Access it:

```bash
minikube service hello-minikube
```

> This will open the app in your default browser.

---

## 🧼 Optional: Stop or Delete

```bash
minikube stop           # Stop the cluster
minikube delete         # Delete the cluster
```

---

## 📚 What's Next?

* Learn YAML configuration files (`deployment.yaml`, `service.yaml`)
* Try `kubectl apply -f` for deploying via YAML
* Explore Helm charts
* Use `minikube dashboard` for a visual interface

---

Would you like a sample `YAML` file to try deploying, or a quick walkthrough of Kubernetes concepts using this Minikube setup?
