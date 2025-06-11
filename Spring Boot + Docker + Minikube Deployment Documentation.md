Here is the **documentation** based on your successful deployment of the Spring Boot application (`DeviceManagement`) on **Minikube** using **Docker** and **Kubernetes**.

---

## 🚀 Spring Boot + Docker + Minikube Deployment Documentation

### 🧰 Prerequisites

* Ubuntu 24.04
* Docker installed and running
* Minikube installed (`v1.36.0`)
* Kubernetes CLI (`kubectl`)
* Java project with a valid `pom.xml` and Spring Boot application (`DeviceManagement`)

---

## 📦 Step 1: Start Minikube with Docker Driver

```bash
minikube start
```

Verify `kubectl` is configured to use the Minikube cluster:

```bash
kubectl config current-context
# Output: minikube
```

---

## 🔁 Step 2: Use Minikube’s Docker Environment

```bash
eval $(minikube docker-env)
```

This allows building Docker images *inside* Minikube’s Docker daemon.

---

## 🏗️ Step 3: Build Docker Image

Navigate to your project directory:

```bash
cd /mnt/d/Thymeleaf/DeviceManagement/DeviceManagement
```

Ensure your `Dockerfile` is present and build the image:

```bash
docker build -t devicemanagement-app .
```

✅ After successful build, the image `devicemanagement-app` will be available to Kubernetes running in Minikube.

---

## 📄 Step 4: Deployment YAML (`deployment.yaml`)

Ensure your `deployment.yaml` looks something like:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: devicemanagement-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: devicemanagement
  template:
    metadata:
      labels:
        app: devicemanagement
    spec:
      containers:
        - name: devicemanagement
          image: devicemanagement-app
          ports:
            - containerPort: 8080
```

Apply it:

```bash
kubectl apply -f deployment/deployment.yaml
```

Check pod status:

```bash
kubectl get pods
```

✅ Pod should show `STATUS: Running`.

---

## 🌐 Step 5: Service YAML (`service.yaml`)

Ensure your service configuration looks like:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: devicemanagement-service
spec:
  type: NodePort
  selector:
    app: devicemanagement
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30080
```

Apply it:

```bash
kubectl apply -f deployment/service.yaml
```

---

## 🌍 Step 6: Access the App in Browser

```bash
minikube service devicemanagement-service
```

If successful, you will get:

```
| default   | devicemanagement-service |        8080 | http://192.168.49.2:30080 |
```

Visit: `http://192.168.49.2:30080/login/login` or the endpoint exposed by your app.

---

## 🧪 Troubleshooting

### ❌ `ImagePullBackOff`

This usually happens when Kubernetes cannot find the image.

✅ You fixed this by ensuring `minikube docker-env` was used **before** building the image.

You can delete old pods to restart:

```bash
kubectl delete pod <pod-name>
```

---

## 📁 Project Structure

Your project should resemble:

```
DeviceManagement/
├── Dockerfile
├── pom.xml
├── src/
├── target/
│   └── DeviceManagement-1.0.jar
├── deployment/
│   ├── deployment.yaml
│   └── service.yaml
```

---

## ✅ Final Check

```bash
kubectl get pods
kubectl get svc
minikube service devicemanagement-service
```

If all is running well, you can now access your Spring Boot application from the exposed Minikube URL.

---
