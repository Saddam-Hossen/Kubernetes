
<details>
<summary><strong>Viw default namespace  </strong></summary>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: devicemanagement-deployment
  namespace: default
spec:
  replicas: 2
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
          image: busybox:latest
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
            initialDelaySeconds: 120
            periodSeconds: 10
            timeoutSeconds: 5
            failureThreshold: 5
          readinessProbe:
            httpGet:
              path: /actuator/health
              port: 3079
            initialDelaySeconds: 90
            periodSeconds: 5
            timeoutSeconds: 5
            failureThreshold: 5
          env:
            - name: SPRING_DATA_MONGODB_URI
              value: "mongodb+srv://root:root@cluster0.lhvo556.mongodb.net/Abc?retryWrites=true&w=majority"
            - name: SPRING_REDIS_HOST
              value: "redis-service.default.svc.cluster.local"
            - name: SPRING_REDIS_PORT
              value: "6379"
            - name: SPRING_REDIS_PASSWORD
              value: "@#12345678"
          resources:
            limits:
              cpu: "500m"
              memory: "512Mi"
            requests:
              cpu: "200m"
              memory: "256Mi"
        - name: log-reader
          image: log-reader-app:latest
          imagePullPolicy: Never
          volumeMounts:
            - name: app-log-volume
              mountPath: /app/logs
          livenessProbe:
            exec:
              command: ["sh", "-c", "test -d /app/logs"]
            initialDelaySeconds: 120
            periodSeconds: 10
            timeoutSeconds: 5
            failureThreshold: 5
          env:
            - name: LOG_READER_PATH
              value: "/app/logs/app.log"
          resources:
            limits:
              cpu: "200m"
              memory: "256Mi"
            requests:
              cpu: "100m"
              memory: "128Mi"
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
<summary><strong>View Custome namespace </strong></summary>

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: team-a
---
# 1.1 ResourceQuota for team-a
apiVersion: v1
kind: ResourceQuota
metadata:
  name: team-a-quota
  namespace: team-a
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
    pods: "5"
    services: "5"
    configmaps: "10"
    secrets: "10"
---
# 2. LimitRange for Namespace
apiVersion: v1
kind: LimitRange
metadata:
  name: team-a-default-limits
  namespace: team-a
spec:
  limits:
    - default:
        cpu: "500m"
        memory: "512Mi"
      defaultRequest:
        cpu: "200m"
        memory: "256Mi"
      type: Container
---

# 3. Secret for sensitive environment variables
apiVersion: v1
kind: Secret
metadata:
  name: devicemanagement-secret
  namespace: team-a
type: Opaque
data:
  SPRING_DATA_MONGODB_URI: bW9uZ29kYitzcnY6Ly9yb290OnJvb3RAZ2V0Q2x1c3Rlci5tb25nb2RiLm5ldC9BYmM/cmV0cnlXcml0ZXM9dHJ1ZSYjd3M9bWFqb3JpdHk=
  SPRING_REDIS_HOST: cmVkaXMtc2VydmljZS5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2Fs
  SPRING_REDIS_PORT: NjM3OQ==
  SPRING_REDIS_PASSWORD: QCMxMjM0NTY3OA==
---



```

</details>

<details>
<summary><strong>Viw custom namepace deployment </strong></summary>

```yaml
# deployment-and-service.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: devicemanagement-deployment
  namespace: team-a
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
          image: busybox:latest
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
          env:
            - name: SPRING_DATA_MONGODB_URI
              value: "mongodb+srv://root:root@cluster0.lhvo556.mongodb.net/Abc?retryWrites=true&w=majority"
            - name: SPRING_REDIS_HOST
              value: "redis-service.default.svc.cluster.local"
            - name: SPRING_REDIS_PORT
              value: "6379"
            - name: SPRING_REDIS_PASSWORD
              value: "@#12345678"
          livenessProbe:
            httpGet:
              path: /actuator/health
              port: 3079
            initialDelaySeconds: 120
            periodSeconds: 10
            timeoutSeconds: 5
            failureThreshold: 5
          readinessProbe:
            httpGet:
              path: /actuator/health
              port: 3079
            initialDelaySeconds: 90
            periodSeconds: 5
            timeoutSeconds: 5
            failureThreshold: 5
          resources:
            limits:
              cpu: "500m"
              memory: "512Mi"
            requests:
              cpu: "200m"
              memory: "256Mi"
        - name: log-reader
          image: log-reader-app:latest
          imagePullPolicy: Never
          volumeMounts:
            - name: app-log-volume
              mountPath: /app/logs
          env:
            - name: LOG_READER_PATH
              value: "/app/logs/app.log"
          livenessProbe:
            exec:
              command: ["sh", "-c", "test -d /app/logs"]
            initialDelaySeconds: 120
            periodSeconds: 10
            timeoutSeconds: 5
            failureThreshold: 5
          resources:
            limits:
              cpu: "200m"
              memory: "256Mi"
            requests:
              cpu: "100m"
              memory: "128Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: devicemanagement-service
  namespace: team-a
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
