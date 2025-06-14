
````markdown
# 🐳 Spring Boot Multi-Container Pod (Kubernetes)

This repository demonstrates how to run **two Spring Boot applications** inside a **single Kubernetes pod** using the **multi-container pod pattern**.  
One application is the **main service**, and the other acts as a **sidecar container for logging**.

---

## 📚 Use Case

Multi-container pods are useful when you need:

- 🧩 **Separation of Concerns**: Business logic vs. logging/monitoring.
- ⚡ **Fast Internal Communication**: Via `localhost` between tightly coupled services.
- 📦 **Simplified Deployment**: Shared lifecycle and resource management.

---

## 🧱 Project Structure

```plaintext
springboot-multi-container-pod/
├── main-app/                 # Business logic Spring Boot application
│   ├── Dockerfile
│   └── src/
├── logger-app/               # Logging Spring Boot application
│   ├── Dockerfile
│   └── src/
├── k8s/
│   └── multi-container-pod.yaml
└── README.md
````

---

## 🛠️ Prerequisites

Before you begin, ensure the following tools are installed:

* 🐳 Docker
* ☸️ Kubernetes (e.g., Minikube, Docker Desktop, GKE)
* 🧪 `kubectl` CLI
* ☕ Java 17+
* 🧰 Maven

---

## 🚧 Build Docker Images

Build Docker images for both applications:

```bash
# Build main app
cd main-app
docker build -t main-app:latest .

# Build logger app
cd ../logger-app
docker build -t logger-app:latest .
```

> ✅ If you're deploying to a remote cluster, push images to a registry (e.g., Docker Hub, ECR, GCR).

---

## ☸️ Deploy to Kubernetes

Apply the Kubernetes deployment manifest:

```bash
kubectl apply -f k8s/multi-container-pod.yaml
```

Verify the pod status:

```bash
kubectl get pods
kubectl describe pod springboot-multi-container
```

Check logs of both containers:

```bash
kubectl logs springboot-multi-container -c main-app
kubectl logs springboot-multi-container -c logger-app
```

---

## 📄 Example Kubernetes Pod YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: springboot-multi-container
spec:
  containers:
    - name: main-app
      image: main-app:latest
      ports:
        - containerPort: 8080
    - name: logger-app
      image: logger-app:latest
      ports:
        - containerPort: 8081
```

---

## 🔁 Inter-Container Communication

Since both containers share the **same network namespace**, they communicate via `localhost`.

### Example (from Main App to Logger App):

```java
RestTemplate rest = new RestTemplate();
rest.postForEntity("http://localhost:8081/log", "Log message", Void.class);
```

---

## ✅ Benefits of Multi-Container Pods

* 🧩 **Modular Design**: Keep components decoupled but co-located.
* 🔒 **Secure by Design**: No need to expose internal services.
* 🚀 **Improved Performance**: Lower latency through localhost.
* 🔧 **Use Cases**: Logging, metrics, proxies, init containers, etc.

---
