# 🚀 GitOps-Based Kubernetes Deployment using ArgoCD

---

## 📌 Project Overview

This project demonstrates a **GitOps-based CI/CD pipeline** using ArgoCD to deploy applications on Kubernetes.

> ✅ No manual `kubectl` changes
> ✅ All deployments happen via Git
> ✅ Fully automated and self-healing system

---

## 🏗️ Architecture

```
Developer → GitHub → ArgoCD → Kubernetes → Application
                          ↓
                    Auto Sync Deployment
```
<img width="800" height="400" alt="image" src="https://github.com/user-attachments/assets/ab8438f7-6a1e-4807-8fed-1ee0cca42a83" />
---

## 🛠️ Technologies Used

* Docker
* Kubernetes (Minikube)
* ArgoCD
* GitHub
* AWS EC2

---

## 📦 Phase 1: Containerization

### Dockerfile

```dockerfile
FROM node:18
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "index.js"]
```

---

### Build Docker Image

```bash
docker build -t kamleshcloud/gitops-app:v1 .
```

---

### Push to Docker Hub

```bash
docker push kamleshcloud/gitops-app:v1
```

---

## ☸️ Phase 2: Kubernetes Setup

### Start Minikube

```bash
minikube start --driver=docker
```

---

### Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: gitops-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: gitops-app
  template:
    metadata:
      labels:
        app: gitops-app
    spec:
      containers:
      - name: gitops-app
        image: kamleshcloud/gitops-app:v1
        ports:
        - containerPort: 3000
```

---

### Service YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: gitops-service
spec:
  type: NodePort
  selector:
    app: gitops-app
  ports:
    - port: 80
      targetPort: 3000
      nodePort: 30007
```

---

## 🔄 Phase 3: ArgoCD Setup

### Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

---

### Access ArgoCD UI

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443 --address 0.0.0.0
```

---

### Get Admin Password

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
```

---

### ArgoCD Application Configuration

* Repository: GitHub repo
* Path: `.`
* Revision: `HEAD`

#### Sync Policy:

* ✅ Automatic Sync
* ✅ Prune Resources
* ✅ Self Heal

---

## 🔁 Phase 4: GitOps Workflow

### Step 1: Modify Application

Update code and create new version.

---

### Step 2: Build & Push Image

```bash
docker build -t kamleshcloud/gitops-app:v2 .
docker push kamleshcloud/gitops-app:v2
```

---

### Step 3: Update Deployment

```yaml
image: kamleshcloud/gitops-app:v2
```

---

### Step 4: Push to GitHub

```bash
git add .
git commit -m "Updated to v2"
git push
```

---

## 🎯 Result

* ArgoCD detects Git changes automatically
* Kubernetes deploys updated version
* No manual deployment required

---

## 🌐 Access Application

Using NodePort:

```
http://<EC2-PUBLIC-IP>:30007
```

---

## 🧠 GitOps Flow

```
Code Change → GitHub → ArgoCD → Kubernetes → Deployment
```

---

## ✅ Key Features

* Fully automated deployment
* No manual `kubectl` usage
* Self-healing infrastructure
* Git as a single source of truth

---

