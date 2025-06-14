
```markdown
# 🐳 Spring Boot Multi-Container Pod (Kubernetes)

This repository demonstrates how to run **two Spring Boot applications** inside a **single Kubernetes pod** using the **multi-container pod pattern**. One application is the main service, while the other acts as a **sidecar container for logging**.

---

## 📚 Use Case

Running multiple containers in one pod can be useful when:
- You want to separate concerns (e.g., business logic vs. logging or monitoring).
- You need fast internal communication between tightly coupled components.
- You prefer simplifying deployment and resource management.

---

## 🧱 Project Structure

```

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

- Docker
- Kubernetes cluster (e.g., Minikube, Docker Desktop, GKE)
- `kubectl` installed and configured
- Java 17+ and Maven (for building apps)

---

## 🚧 Build Docker Images

Build both Spring Boot apps as Docker images:

```bash
cd main-app
docker build -t main-app:latest .

cd ../logger-app
docker build -t logger-app:latest .
````

Push to your container registry if using a cloud Kubernetes cluster.

---

## ☸️ Kubernetes Deployment

Apply the deployment manifest:

```bash
kubectl apply -f k8s/multi-container-pod.yaml
```

Check pod status:

```bash
kubectl get pods
kubectl describe pod springboot-multi-container
```

View logs from each container:

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

Since both containers share the same **network namespace**, they can communicate over `localhost`:

```java
// From main-app to logger-app
RestTemplate rest = new RestTemplate();
rest.postForEntity("http://localhost:8081/log", "Log message", Void.class);
```

---

## ✅ Benefits of Multi-Container Pod Pattern

* 🧩 **Modular Design:** Separate services within the same pod.
* 🔒 **Secure Communication:** Uses `localhost` internally.
* 🚀 **Performance:** No need for service discovery or networking overhead.
* 🛠️ **Sidecar Use Cases:** Logging, monitoring, proxying, caching, etc.

---
