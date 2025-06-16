
---

# 📦 Device Management Kubernetes Deployment

This repository provides Kubernetes manifests to deploy a **multi-container Pod** running:

* A **Spring Boot Device Management application**
* A **sidecar Log Reader** to monitor application logs in real time

## 🚀 Overview

The `devicemanagement-deployment` pod includes:

| Container          | Description                                                                                  |
| ------------------ | -------------------------------------------------------------------------------------------- |
| `devicemanagement` | Spring Boot application (`dvm-app:latest`) that writes logs to `/app/logs/app.log`           |
| `log-reader`       | Sidecar container (`log-reader-app:latest`) that monitors and processes the application logs |

✅ The containers **share a volume** using `emptyDir`, mounted at `/app/logs`.

---

## 🧰 Prerequisites

* Kubernetes cluster (e.g., **Minikube**)
* `kubectl` configured
* Docker images `dvm-app:latest` and `log-reader-app:latest` built and available in the cluster

---

## ⚙️ Deployment Instructions

### 1. Build and Load Docker Images

#### Device Management App:

```bash
cd /mnt/d/Thymeleaf/DeviceManagement/DeviceManagement
./mvnw clean package
docker buildx build -t dvm-app .
docker save -o dvm-app.tar dvm-app
minikube image load dvm-app.tar
```

#### Log Reader App:

```bash
cd /mnt/d/Thymeleaf/DeviceManagement/log-reader-app
./mvnw clean package
docker buildx build -t log-reader-app .
docker save -o log-reader-app.tar log-reader-app
minikube image load log-reader-app.tar
```

---

### 2. Apply Kubernetes Manifests

```bash
kubectl apply -f k8s/devicemanagement.yaml
```

---

### 3. Verify Deployment

```bash
kubectl get pods -n default
kubectl logs pod/devicemanagement-deployment-<pod-name> -c log-reader -n default
kubectl port-forward pod/devicemanagement-deployment-<pod-name> 3079:3079 -n default
curl http://127.0.0.1:3079/actuator/health
```

---

### 4. Access the Service

* Start the Minikube tunnel:

  ```bash
  minikube tunnel
  ```

* Access the application via browser:

  ```
  http://192.168.49.2:30080
  OR
  http://127.0.0.1:34587
  ```

---

## 📚 References

* [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/)
* [Sharing Volumes Between Containers](https://kubernetes.io/docs/tasks/access-application-cluster/communicate-containers-same-pod-shared-volume/)
* [Kubernetes GitHub](https://github.com/kubernetes/kubernetes)
* [Kubernetes Official Docs](https://kubernetes.io/)

---


## 📄 Kubernetes Manifest

The following YAML defines the **Deployment** and **Service** for the Device Management application and its sidecar log reader. This configuration:

* Uses an `initContainer` to initialize the `/app/logs` directory with appropriate permissions
* Mounts a shared `emptyDir` volume to enable log sharing between containers
* Exposes the service via `NodePort` on port `30080`

<details>
<summary><strong>Viw deployment.yml </strong></summary>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: devicemanagement-deployment
  namespace: default
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
      initContainers:
        - name: init-logs
          image: busybox
          command: ["sh", "-c", "mkdir -p /app/logs && chmod -R 777 /app/logs"]
          volumeMounts:
            - name: app-log-volume
              mountPath: /app/logs
      volumes:
        - name: app-log-volume
          emptyDir: {}
      containers:
        - name: devicemanagement
          image: dvm-app:latest
          imagePullPolicy: Never
          ports:
            - containerPort: 3079
          volumeMounts:
            - name: app-log-volume
              mountPath: /app/logs
          livenessProbe:
            httpGet:
              path: /actuator/health
              port: 3079
            initialDelaySeconds: 90
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /actuator/health
              port: 3079
            initialDelaySeconds: 60
            periodSeconds: 5
        - name: log-reader
          image: log-reader-app:latest
          imagePullPolicy: Never
          volumeMounts:
            - name: app-log-volume
              mountPath: /app/logs
          livenessProbe:
            exec:
              command: ["sh", "-c", "test -d /app/logs"]
            initialDelaySeconds: 90
            periodSeconds: 10
---
apiVersion: v1
kind: Service
metadata:
  name: devicemanagement-service
  namespace: default
spec:
  type: NodePort
  selector:
    app: devicemanagement
  ports:
    - port: 3079
      targetPort: 3079
      nodePort: 30080
```

</details>


<details>
<summary><strong>Logger Docker file</strong></summary>

```yaml
FROM eclipse-temurin:21-jdk
COPY target/log-reader-app-1.0.0.jar app.jar
EXPOSE 3080
ENTRYPOINT ["java", "-Dlog.reader.path=app/logs/app.log", "-jar", "/app.jar"]

```

</details>

<details>
  
<summary><strong>DeviceManagement Docker file</strong></summary>

```yaml
# Use an official OpenJDK runtime as a parent image
FROM eclipse-temurin:21-jdk-jammy

# Set the working directory inside the container
WORKDIR /app

# Copy the built JAR file into the container
COPY target/DeviceManagement-1.0.jar app.jar

# Expose the port your application runs on
EXPOSE 3079

# Command to run the application
ENTRYPOINT ["java", "-jar", "app.jar"]

```

</details>

<details>
<summary><strong> DeviceManagement application.propereties</strong></summary>

```yaml
spring.data.mongodb.database=Abc
spring.data.mongodb.uri=
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
server.port=3079
spring.profiles.active=prod
#
## kafka
#spring.kafka.bootstrap-servers=localhost:9092
#spring.kafka.consumer.group-id=my-group
#spring.kafka.consumer.auto-offset-reset=earliest
#spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
#spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
#spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
#spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer

# Redis connection
spring.cache.type=redis
#spring.redis.host=127.0.0.1
#spring.redis.host=localhost
spring.redis.host=redis-service.default.svc.cluster.local
spring.redis.port=6379
spring.redis.password=@#12345678

# Optional: connection pool
spring.redis.lettuce.pool.max-active=10
spring.redis.lettuce.pool.max-idle=5
spring.redis.lettuce.pool.min-idle=1

# Enable Log4j2
logging.config=classpath:log4j2.properties

status = warn
name = FileJsonLogger

# File appender writing JSON logs
appender.file.type = File
appender.file.name = JsonFileAppender
appender.file.fileName = /app/logs/app.log
appender.file.layout.type = JsonLayout
appender.file.layout.compact = true
appender.file.layout.eventEol = true

# Root logger config
rootLogger.level = info
rootLogger.appenderRefs = file
rootLogger.appenderRef.file.ref = JsonFileAppender

management.endpoints.web.exposure.include=health


```

</details>

<details>
<summary><strong>Logger appication.properties</strong></summary>

```yaml
logging.level.root=INFO
server.port=3080
log.reader.path=/app/logs/app.log


```

</details>


