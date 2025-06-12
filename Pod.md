In Kubernetes, **a Pod can contain one or more containers**.

### 📌 Key Points:

* **Single-container Pod** is the most common case (like running just one application).
* **Multi-container Pods** are used when containers need to share:

  * The same **network namespace** (i.e., same IP, port space)
  * The same **storage volumes**
  * Need to communicate via local `localhost`

### 🧠 Why use multiple containers in a Pod?

For tightly coupled helper processes. Example:

* A main app + a log forwarder
* A web server + a cache warm-up container

### 🔍 Check how many containers are in a Pod:

Run:

```bash
kubectl get pod <pod-name> -o jsonpath="{.spec.containers[*].name}"
```

Example:

```bash
kubectl get pod my-pod -o jsonpath="{.spec.containers[*].name}"
```

It will list all container names inside the Pod.

---

