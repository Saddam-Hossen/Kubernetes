
# Device Management Deployment with Docker & Minikube

This project demonstrates how to containerize a Spring Boot application and deploy it on a local Kubernetes cluster using **Docker**, **Minikube**, and **kubectl**.

---

## 📦 Prerequisites

* [Docker](https://www.docker.com/)
* [Minikube](https://minikube.sigs.k8s.io/docs/start/)
* [kubectl](https://kubernetes.io/docs/tasks/tools/)
* Java 21 (Temurin recommended)
* Maven or Gradle to build the Spring Boot project

---

## 🚀 Getting Started

### Step 1: Start Minikube

```bash
minikube start
```

---

### Step 2: Use Minikube’s Docker Environment

This sets your terminal to use Minikube’s Docker daemon:

```bash
eval $(minikube docker-env)
```

---

### Step 3: Navigate to Your Project Directory

```bash
cd /mnt/d/Thymeleaf/DeviceManagement/DeviceManagement
```

---

### Step 4: Build the JAR File

```bash
mvn clean package
```

This creates the JAR file under the `target/` directory.

---

### Step 5: Create Docker Image

```bash
docker buildx build -t dvm-app .
```

Check the image:

```bash
docker images
```

---

### Step 6: Export and Load Docker Image into Minikube

If you are **not using `eval $(minikube docker-env)`**, you must load the image manually:

```bash
docker save -o dvm-app.tar dvm-app
minikube image load dvm-app.tar
```

---

### Step 7: Deploy to Minikube

```bash
kubectl apply -f deployment/deployment.yaml
```

Check the deployment:

```bash
kubectl get all
```

---

### Step 8: Access the Application

```bash
minikube service <your-service-name>
```

Replace `<your-service-name>` with the name defined in your `deployment.yaml`.

---

## 📁 Project Structure

```
DeviceManagement/
├── deployment/
│   └── deployment.yaml         # Kubernetes deployment config
├── Dockerfile                  # Docker build instructions
├── target/
│   └── DeviceManagement-1.0.jar  # Built JAR file
└── README.md
```

---

## 🛠 Useful Commands

| Command                                       | Description                  |
| --------------------------------------------- | ---------------------------- |
| `minikube start`                              | Start Minikube cluster       |
| `eval $(minikube docker-env)`                 | Use Minikube’s Docker daemon |
| `docker buildx build -t dvm-app .`            | Build Docker image           |
| `docker save -o dvm-app.tar dvm-app`          | Save Docker image            |
| `minikube image load dvm-app.tar`             | Load image into Minikube     |
| `kubectl apply -f deployment/deployment.yaml` | Deploy app                   |
| `kubectl get all`                             | List all resources           |
| `minikube service <service-name>`             | Open service in browser      |

---

## ✅ License

This project is open-source and available under the [MIT License](LICENSE).

---

