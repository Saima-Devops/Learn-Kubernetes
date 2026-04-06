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
kubectl -f replicasets.yml
```

---

- ### Verify

```bash
kubectl get replicasets -n nginx
kubectl get pods -n nginx
```



----
