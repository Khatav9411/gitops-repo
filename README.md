GitOps-Based Kubernetes Deployment using ArgoCD
📌 Project Overview

This project demonstrates a GitOps workflow for deploying applications on Kubernetes using ArgoCD.

The goal is to eliminate manual deployments and ensure:

All changes are made via Git and automatically applied to Kubernetes.

🏗️ Architecture
Developer → GitHub → ArgoCD → Kubernetes → Application
                          ↓
                    Auto Sync Deployment

<img width="1000" height="600" alt="image" src="https://github.com/user-attachments/assets/ab8438f7-6a1e-4807-8fed-1ee0cca42a83" />


🛠️ Technologies Used
Docker
Kubernetes (Minikube)
ArgoCD
GitHub
AWS EC2

📦 Phase 1: Containerization
1. Create Dockerfile
FROM node:18
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "index.js"]
2. Build Docker Image
docker build -t kamleshcloud/gitops-app:v1 .
3. Push to Docker Hub
docker push kamleshcloud/gitops-app:v1
☸️ Phase 2: Kubernetes Setup
1. Start Minikube
minikube start --driver=docker
2. Deployment YAML
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
3. Service YAML
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
🔄 Phase 3: ArgoCD Setup
1. Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
2. Access ArgoCD UI
kubectl port-forward svc/argocd-server -n argocd 8080:443 --address 0.0.0.0
3. Get Admin Password
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
4. Create Application
Repo: GitHub repository
Path: .
Sync Policy: Automatic
Enable:
Auto Sync
Prune
Self Heal
🔁 Phase 4: GitOps Workflow
Update Application Version
Modify application code
Build new image:
docker build -t kamleshcloud/gitops-app:v2 .
docker push kamleshcloud/gitops-app:v2
Update deployment.yaml:
image: kamleshcloud/gitops-app:v2
Push to GitHub:
git add .
git commit -m "Updated to v2"
git push
🎯 Result
ArgoCD detects change automatically
Kubernetes deploys new version
No manual kubectl required
🌐 Access Application

Using NodePort:

http://<EC2-PUBLIC-IP>:30007
🧠 GitOps Flow
Code Change → GitHub → ArgoCD → Kubernetes → Deployment
✅ Key Features
Fully automated deployment
No manual kubectl changes
Self-healing infrastructure
Git as single source of truth
