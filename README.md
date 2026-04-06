# Setup Nginx Server on EC2 with Kind (Kubernetes IN Docker) Type of Cluster

## 🧱 What is Kind?

A tool to run Kubernetes clusters using Docker containers as nodes

- **Cluster type:** Local / development cluster

- **Use case:**
    - Testing Kubernetes configs
    - CI/CD pipelines

- **Key idea:** Each “node” is just a Docker container

- **Lightweight, fast, and ideal for developers**

---

## Overview

This guide demonstrates how to:

- Launch an Ubuntu EC2 instance
- Install Docker and kind
- Create a Kubernetes cluster using kind
- Deploy an Nginx server in a dedicated namespace
- Verify the cluster and pod status

Ideal for development or testing Kubernetes workloads on AWS EC2.

-----

## Setup Nginx Server on EC2 with kind (1 Master + 2 Workers)

### Prerequisites

- AWS account
- EC2 instance: Ubuntu 22.04 LTS, t3.medium+ recommended
- Security group: allow outbound internet traffic and SSH access
- `sudo` privileges on the instance

---

## Step 1: Connect to EC2

SSH into your EC2 instance:

```bash
ssh -i "your-key.pem" ubuntu@<EC2_PUBLIC_IP>
```

**Update packages:**

```bash
sudo apt update && sudo apt upgrade -y
```
---

## Step 2: Install Docker

```bash
sudo apt install -y docker.io curl conntrack iptables
sudo systemctl enable docker
sudo systemctl start docker
```

**Configure Docker for Kubernetes:**

```bash
sudo mkdir -p /etc/docker
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
EOF
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl restart docker
```
---

## Step 3: Fix system settings for kind

**a) Set iptables to legacy mode**

```bash
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
iptables --version
```

**b) Enable kernel networking**

```bash
sudo modprobe br_netfilter
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
sudo sysctl --system
```

**c) Increase file limits**

```bash
ulimit -n 1048576
echo "* soft nofile 1048576" | sudo tee -a /etc/security/limits.conf
echo "* hard nofile 1048576" | sudo tee -a /etc/security/limits.conf
```

---

## Step 4: Install kubectl and kind

```bash
Install kubectl
curl -LO https://dl.k8s.io/release/v1.29.0/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
Install kind
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-amd64
chmod +x kind
sudo mv kind /usr/local/bin/
```

---

## Step 5: Create a kind cluster with 1 master + 2 workers

**Create a kind-cluster.yaml:**

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

**Create the cluster:**

```bash
kind create cluster --config kind-cluster.yaml --image kindest/node:v1.29.0
kubectl get nodes
kubectl get pods -n kube-system
```

Ensure all system pods (kube-proxy, coredns, etc.) are **Running.**

---

## Step 6: Preload Nginx Docker image

```bash
docker pull nginx:latest
kind load docker-image nginx:latest
```

> Avoids ImagePullBackOff errors on EC2 instances with limited internet access.

---

## Step 7: Deploy Nginx in a dedicated namespace

Create **nginx-namespace-pod.yaml** with `nodeSelector`:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nginx
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: nginx
spec:
  nodeSelector:
    node-role.kubernetes.io/worker: ""
  containers:
    - name: nginx
      image: nginx:latest
      imagePullPolicy: IfNotPresent
      ports:
        - containerPort: 80
```

### Importance of nodeSelector:

Ensures Nginx pods run only on worker nodes, not on the master/control-plane.
Helps mimic production environments where the control-plane is reserved for system pods.
Improves resource management and avoids scheduling conflicts.

---

### Apply the manifest:

```bash
kubectl apply -f nginx-namespace-pod.yaml
kubectl get pods -n nginx
```

> Wait until the pod is **Running.**

---

## Step 8: Optional — Expose Nginx outside the cluster

```bash
kubectl expose pod nginx -n nginx --type=NodePort --port=80
kubectl get svc -n nginx
```

----

Access **Nginx** from EC2 public IP:

http://<EC2_PUBLIC_IP>:<NODE_PORT>

----

✅ Your Nginx server is now running on a kind Kubernetes cluster with 1 master + 2 workers on EC2, safely scheduled on worker nodes!

----
