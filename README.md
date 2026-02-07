🚀 Blue-Green Deployment on AKS with Jenkins (Production-Grade)

This project demonstrates a zero-downtime Blue-Green deployment strategy on Azure Kubernetes Service (AKS) using Jenkins CI/CD, Docker, and NGINX Ingress.

It is designed to reflect real-world DevOps practices used in production environments.

🧠 Architecture Overview

Traffic Flow:

User → Ingress → Kubernetes Service → Blue / Green Pods


Deployment Strategy:

Two environments: Blue & Green

Both run in separate namespaces

Traffic is switched by updating the Service selector

Zero downtime ensured via Ingress + Service abstraction

🛠 Tech Stack

Cloud: Azure (AKS)

CI/CD: Jenkins (Multistage Pipeline)

Containerization: Docker

Orchestration: Kubernetes

Ingress: NGINX Ingress Controller

Deployment Strategy: Blue-Green

Version Control: GitHub

📁 Project Structure
blue-green-aks/
│
├── app/
│   ├── index.html
│   └── Dockerfile
│
├── k8s/
│   ├── namespaces.yaml
│   ├── blue/
│   │   └── deployment.yaml
│   ├── green/
│   │   └── deployment.yaml
│   └── prod/
│       ├── service.yaml
│       └── ingress.yaml
│
├── Jenkinsfile
│
└── README.md

⚙️ Prerequisites

Ensure the following tools are installed and configured:

Azure CLI (az)

Docker

kubectl

Helm

Jenkins Server

AKS Cluster (South India or any region)

☸️ Kubernetes Setup
1️⃣ Create Namespaces
kubectl apply -f k8s/namespaces.yaml


Namespaces created:

app-blue

app-green

prod

2️⃣ Install NGINX Ingress Controller
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx \
  --create-namespace

🐳 Docker Image

The application is a simple NGINX-based web app.

Dockerfile highlights:

Lightweight (nginx:alpine)

Immutable images

Tagged with Jenkins build number

🤖 Jenkins CI/CD Pipeline
Jenkins Stages

Checkout Code

Build Docker Image

Push Image to Docker Hub

Deploy GREEN Environment

Health Check

Switch Traffic (Blue → Green)

Cleanup Blue

Required Jenkins Credentials
Credential	Type	Purpose
dockerhub-creds	Username/Password	Push Docker images
aks-kubeconfig	Secret File	Access AKS cluster
🔁 Blue-Green Deployment Flow

Existing traffic runs on BLUE

Jenkins deploys new version to GREEN

Health check validates GREEN

Service selector switches traffic to GREEN

BLUE environment is safely removed

Zero downtime achieved 🎯

🌐 Access Application
kubectl get svc -n ingress-nginx


Use the EXTERNAL-IP to access the app.

(Optional) Map domain:

app.example.com → External IP

🧪 Verification Commands
kubectl get pods -n app-green
kubectl get svc -n prod
kubectl describe ingress -n prod

💬 Interview Explanation (One-Liner)

“We implemented Blue-Green deployment on AKS using Jenkins where traffic is switched via Kubernetes Service selectors behind an NGINX Ingress, ensuring zero downtime.”

🔥 This line alone is interview gold.

🚀 Future Enhancements

Canary + Blue-Green Hybrid

Helm-based deployments

ArgoCD GitOps

Trivy security scanning

Automated rollback

HPA & monitoring (Prometheus + Grafana)

🧑‍💻 Author

Abhishek Pandey
DevOps Engineer | AWS | Azure | Kubernetes | Jenkins | Terraform
