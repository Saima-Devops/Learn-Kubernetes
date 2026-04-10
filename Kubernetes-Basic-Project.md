# What is Kubernetes?

Kubernetes is a system that:

- Runs your containers
- Keeps them alive
- Scales them
- Connects them

------

## Core Concepts

### 1. Pod (smallest unit)

**A Pod = 1 or more containers**

Example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
  - name: app
    image: nginx
```

-----

### 2. Deployment (manages Pods)

- You never run pods directly in real life
- You use Deployments

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app
        image: nginx
```

----

### 3. Service (exposes your app)

👉 Pods change IP → Service gives stable access

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 80
  type: ClusterIP
```

----

## Set up a mini Project with the help of K8s 

**Setup (Do This First)**

Choose ONE:

**Option A (Easiest)**

```bash
Install Docker Desktop → Enable Kubernetes
```

**Option B**
Install:

```bash
kubectl
minikube
```

## MINI PROJECT — Build & Deploy App

### STEP#1 - Create a Simple App

Example `Node.js`:

```JavaScript
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.send("Hello from Kubernetes 🚀");
});

app.listen(3000);
```

-----

### STEP#2 - Dockerize It (Make a Dcoker Image)

```Dcokerfile
FROM node:18
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "index.js"]
```

**Build & push:**

```bash
docker build -t your-dockerhub-username/kube-demo .
docker push your-dockerhub-username/kube-demo
```

---

### STEP#3 -  Deploy to Kubernetes

**Deployment:**

```Yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kube-demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: kube-demo
  template:
    metadata:
      labels:
        app: kube-demo
    spec:
      containers:
      - name: app
        image: your-dockerhub-username/kube-demo
        ports:
        - containerPort: 3000
````

**Apply:**

```bash
kubectl apply -f deployment.yaml
```
----

### STEP#4 -  Expose It through Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: kube-demo-service
spec:
  type: NodePort
  selector:
    app: kube-demo
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 30007
```

### STEP#5 -  Access your App

```bash
minikube service kube-demo-service
```

OR:

```bash
kubectl get nodes -o wide
```

Then open:

```bash
http://<node-ip>:30007
```bash

---
