saddamnvn@DESKTOP-PTTGD06:~$ kubectl get all
NAME                                  READY   STATUS        RESTARTS        AGE
pod/dvm-deployment-7d86c9c95c-pqkbk   0/4     Terminating   0               4h11m
pod/nginx-5869d7778c-pvnd2            1/1     Running       4 (4h13m ago)   18h



Ah, I see the issue now:

You still have the pod `dvm-deployment-7d86c9c95c-pqkbk` stuck in **Terminating** state for **4 hours**, but the deployment `dvm-deployment` no longer exists (not listed in `kubectl get deployments`).

---

### Why is this happening?

* Kubernetes sometimes has trouble fully terminating pods if there are issues like stuck finalizers or problems communicating with the node.
* Since the deployment is gone but the pod remains terminating, Kubernetes is "stuck" trying to clean it up but can't.

---

### How to force delete the stuck pod?

You can try:

```bash
kubectl delete pod dvm-deployment-7d86c9c95c-pqkbk --grace-period=0 --force
```

This command forcibly removes the pod immediately without waiting for graceful shutdown.

---

### After that

* Run `kubectl get pods` again to confirm it's gone.
* Make sure your deployment YAML is ready and then re-apply it if needed.
* Check image availability and pod status carefully.

---

If you want, I can help you with any of these steps!
