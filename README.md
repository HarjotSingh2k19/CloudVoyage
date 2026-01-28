# ☁️ CloudVoyage: Full-Stack Microservices on Kubernetes

![Kubernetes](https://img.shields.io/badge/Kubernetes-Production%20Ready-blue?logo=kubernetes)
![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?logo=docker)
![React](https://img.shields.io/badge/Frontend-React-61DAFB?logo=react)
![Node.js](https://img.shields.io/badge/Backend-Node.js-339933?logo=node.js)
![MongoDB](https://img.shields.io/badge/Database-MongoDB-47A248?logo=mongodb)

Welcome to CloudVoyage, a scalable task management application built to demonstrate the journey from local containerization to advanced cloud orchestration.

This project serves as a comprehensive reference architecture for migrating a Full-Stack MERN application (MongoDB, Express, React, Node) from Docker Compose to a production-grade Kubernetes Cluster with Auto-Scaling, Ingress Routing, and Self-Healing capabilities.

## 📝 Table of Contents

- [Introduction](#-introduction)
- [Features](#-features)
- [Tech Stack](#️-tech-stack)
- [Architecture](#-architecture)
- [Prerequisites](#-prerequisites)
- [Phase 1: Local Development (Docker)](#-phase-1-local-development-docker)
- [Phase 2: Production Deployment (Kubernetes)](#-phase-2-production-deployment-kubernetes)
- [Advanced Verification (HPA & CronJobs)](#-advanced-verification-hpa--cronjobs)
- [Project Structure](#-project-structure)

---

## 📝 Introduction

CloudVoyage is a travel-themed task manager designed to handle real-world production scenarios. Unlike simple "Hello World" tutorials, this project tackles hard problems like:
* Networking: How to route traffic using domains instead of IP addresses.
* Persistence: How to keep database data safe when pods crash.
* Scaling: How to handle sudden traffic spikes automatically.

---

## ✨ Features

### 🐳 Docker & Local Development
* Multi-Container Setup: One-click startup using `docker-compose` for local testing.
* Optimized Images: Custom `Dockerfile` configurations for Node.js (Backend) and React (Frontend).
* Hot Reloading: Configured for rapid development cycles.

### ☸️ Kubernetes & Production
* Ingress Controller: Domain-based routing (`wanderlust.local`) replacing raw NodePorts.
* Stateful Architecture: MongoDB deployed as a StatefulSet with Persistent Volume Claims (PVC) to ensure zero data loss.
* Auto-Scaling (HPA): Backend automatically scales from 2 to 10 replicas based on CPU load.
* Automated Backups: A CronJob runs daily at midnight to secure database records.
* Secret Management: Secure handling of database credentials using K8s Secrets.

---

## 🛠️ Tech Stack

* Frontend: React.js, Nginx (Web Server)
* Backend: Node.js, Express REST API
* Database: MongoDB
* Containerization: Docker Desktop & Docker Compose
* Orchestration: Kubernetes (Minikube)
* Networking: Nginx Ingress Controller
* Monitoring: Kubernetes Metrics Server

---

## 🏗️ Architecture

```mermaid
graph TD
    User(User Browser) -->|[http://wanderlust.local](http://wanderlust.local)| Ingress(Nginx Ingress Controller)
    Ingress -->|Routes Traffic| Frontend(React Frontend Service)
    Frontend -->|API Calls| Backend(Node.js Backend Service)
    Backend -->|Read/Write| DB[(MongoDB StatefulSet)]
    
    subgraph "Resilience & Scaling"
        HPA(Horizontal Pod Autoscaler) -.->|Auto-Scale| Backend
        Cron(Backup CronJob) -.->|Daily Backup| DB
    end


🔧 Prerequisites
Before starting, ensure you have the following tools installed:

Docker Desktop (Required for building images and local testing)
Minikube (Required for the local Kubernetes cluster)
Kubectl (CLI tool to interact with the cluster)
Git (Version control)

🐳 Phase 1: Local Development (Docker)
Before deploying to the cloud, we run the app locally to ensure the code works.

1. Clone the Repository

Bash
git clone [https://github.com/HarjotSingh2k19/CloudVoyage.git](https://github.com/HarjotSingh2k19/CloudVoyage.git)
cd CloudVoyage
2. Run with Docker Compose This spins up the Frontend, Backend, and Database in isolated containers.

Bash
docker-compose up --build
3. Access the App

Frontend UI: http://localhost:3000

Backend API: http://localhost:3500

To stop the local containers, press Ctrl+C or run docker-compose down.

☸️ Phase 2: Production Deployment (Kubernetes)
Now we move to the "Pro" level. We will deploy the infrastructure layer by layer.

1. Start the Cluster & Enable Addons
We need the Ingress Controller (Router) and Metrics Server (Monitor).

Bash
minikube start
minikube addons enable ingress
minikube addons enable metrics-server

2. Deploy the Database Layer (Foundation)
Sets up MongoDB StatefulSet, Services, and Secrets.

Bash
kubectl apply -f k8s/database/
(Wait ~30 seconds for the database pod to start)

3. Deploy the Application Layer
Deploys the Node.js Backend and React Frontend.

Bash
kubectl apply -f k8s/backend/
kubectl apply -f k8s/frontend/

4. Configure Networking (Ingress)
Enables the custom domain routing.

Bash
kubectl apply -f k8s/ingress.yaml

5. 🌐 Network Bridge (Crucial Step!)
Since wanderlust.local is a custom domain, we must map it to the cluster.

Start Tunnel: Open a new terminal and run:

Bash
sudo minikube tunnel
Update Hosts File: Run sudo nano /etc/hosts and add:

Plaintext
127.0.0.1 wanderlust.local
Access App: Open browser at http://wanderlust.local

🧪 Advanced Verification (HPA & CronJobs)
📈 Test Auto-Scaling
We simulate a traffic spike to verify the backend adds more pods automatically.

Monitor Scaling:

Bash
kubectl get hpa -n cloudvoyage -w
Launch Load Generator:

Bash
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -n cloudvoyage -- /bin/sh -c "while true; do wget -q -O- http://backend-service:3500/ok; done"
Result: Watch the REPLICAS count jump from 2 to 5+.

🕒 Test Automated Backups
Trigger the nightly backup job manually.

Bash
kubectl create job --from=cronjob/mongo-backup manual-test -n cloudvoyage
kubectl logs -n cloudvoyage job/manual-test
Output: "Backup Complete!"

📂 Project Structure
Plaintext
CloudVoyage/
├── backend/                 # Node.js Source Code & Dockerfile
├── frontend/                # React Source Code & Dockerfile
├── docker-compose.yml       # Local Development Config
├── k8s/                     # Kubernetes Manifests
│   ├── database/            # StatefulSet, PVC, Secrets, CronJob
│   ├── backend/             # Deployment, Service, ConfigMap, HPA
│   ├── frontend/            # Deployment, Service
│   └── ingress.yaml         # Ingress Rules
└── README.md                # Documentation

🤝 Contributing
Contributions are welcome! Please fork the repository and submit a pull request for review.

👨‍💻 Author
Harjot Singh GitHub: @HarjotSingh2k19

Built as a Capstone Project to master Cloud-Native Architecture.