# Instructions to Deploy DaemonSet and CronJob for ToDo App

## 📦 Namespace Setup

First, make sure the `mateapp` namespace exists:
```
kubectl create namespace mateapp
```
> Note: The ToDo app is assumed to be deployed in `todoapp` namespace and exposed via a ClusterIP service `todoapp-service`.

---

## 🚀 Deploying DaemonSet

Apply the DaemonSet configuration:
```
kubectl apply -f daemonset.yml
```
This will deploy a `DaemonSet` that runs on **each node** in the cluster, sending a `curl` request every 5 seconds to:

http://todoapp-service.todoapp.svc.cluster.local

Make sure your DaemonSet container runs an infinite loop that performs `curl` every 5 seconds. For example:

command: ["sh", "-c", "while true; do curl http://todoapp-service.todoapp.svc.cluster.local; sleep 5; done"]

### 🔍 View logs

You can inspect logs using:
```
kubectl logs -n mateapp -l app=mate-server --tail=20 --follow
```
---

## 🕓 Deploying CronJob

Apply the CronJob configuration:
```
kubectl apply -f cronjob.yml
```
This CronJob will call the `/api/health` endpoint every **4 minutes**.

### 🔍 View recent jobs
```
kubectl get jobs -n mateapp
```
### 🔍 View logs for a job

First, find the pod:
```
kubectl get pods -n mateapp --selector=job-name=<job-name>
```
Then check logs:
```
kubectl logs <pod-name> -n mateapp
```
---

## ✅ Validation

- Confirm that the DaemonSet logs contain `curl` responses every 5 seconds.
- Confirm that the CronJob creates jobs every 4 minutes and that logs show a successful `/api/health` response.
- Use:
```
kubectl describe cronjob health-cronjob -n mateapp
```
To see job history limits and concurrency settings.

---

## 🔁 Reapply or Delete

To reapply configs after changes:
```
kubectl apply -f daemonset.yml
kubectl apply -f cronjob.yml
```
To delete:
```
kubectl delete -f daemonset.yml
kubectl delete -f cronjob.yml
```
---

Happy deploying! 🚀
