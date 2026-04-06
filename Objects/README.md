## How to create a Pod?

```bash
kubectl apply -f pod.yml
```

----

## How to create a Namespace?

```bash
kubectl create namespace nginx
```

----

## How to create a Deployment?

```bash
kubectl apply -f deployment.yml
```
----

- ### Verify Deployment

```bash
kubectl get deployments
kubectl get pods
kubectl describe deployment nginx-deployment
```
----

- ### Update Deployment

**Example: change image version**

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.25
```

----

- ### Scale Deployment

```bash
kubectl scale deployment nginx-deployment --replicas=5
```
----

- ### Delete Deployment

```bash
kubectl delete deployment nginx-deployment
```
OR

```bash
kubectl delete -f deployment.yml
```

----

## How to create a ReplicaSet?

```bash
kubectl apply -f replicasets.yml
```

---

- ### Verify

```bash
kubectl get replicasets -n nginx
kubectl get pods -n nginx
```
---

## How to create a DaemonSet?

```bash
kubectl apply -f daemonsets.yml
```
----

## How to create a Job?

```bash
kubectl apply -f job.yml
```
----

- ### Verify

```bash
kubectl get jobs -n nginx
kubectl get pods -n nginx
kubectl logs pod/demo-job-pq6rx -n nginx
```

---

## How to create a CronJob?

```bash
kubectl apply -f cron-job.yml
```

- ### Verify

```bash
kubectl get pods -n nginx
kubectl logs pod/minute-backup-29591773-wq6f6
```

- ### Delete CronJob

```bash
kubectl delete -f cron-job.yaml
```

- ### Delete Pods

```bash
  kubectl delete pod/minute-backup-29591773-wq6f6 -n nginx
```

----
