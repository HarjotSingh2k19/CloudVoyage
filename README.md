# CloudVoyage: Full-Stack Microservices on Kubernetes

Welcome to CloudVoyage, a scalable task management application built to demonstrate the journey from local containerization to advanced cloud orchestration.

This project serves as a comprehensive reference architecture for migrating a Full-Stack MERN application (MongoDB, Express, React, Node) from Docker Compose to a production-grade Kubernetes cluster with Auto-Scaling, Ingress Routing, and Self-Healing capabilities.

---

## 📝 Table of Contents

* Introduction
* Features
* Tech Stack
* Architecture
* Prerequisites
* Phase 1: Local Development (Docker)
* Phase 2: Production Deployment (Kubernetes)
* Advanced Verification (HPA & CronJobs)
* Project Structure

---

## 📝 Introduction

CloudVoyage is a travel-themed task manager designed to handle real-world production scenarios. Unlike simple *"Hello World"* tutorials, this project tackles hard problems like:

* Networking: Routing traffic using domains instead of IP addresses
* Persistence: Keeping database data safe when pods crash
* Scaling: Handling sudden traffic spikes automatically

---

## ✨ Features

### 🐳 Docker & Local Development

* Multi-Container Setup: One-click startup using `docker-compose`
* Optimized Images: Custom `Dockerfile`s for Node.js (Backend) and React (Frontend)
* Hot Reloading: Faster local development cycles

### ☸️ Kubernetes & Production

* Ingress Controller: Domain-based routing (`wanderlust.local`) instead of NodePorts
* Stateful Architecture: MongoDB deployed as a StatefulSet with PVCs
* Auto-Scaling (HPA): Backend scales from 2 to 10 replicas based on CPU
* Automated Backups: Daily MongoDB backups using a CronJob
* Secret Management: Secure credentials via Kubernetes Secrets

---

## 🛠️ Tech Stack

* Frontend: React.js, Nginx
* Backend: Node.js, Express (REST API)
* Database: MongoDB
* Containerization: Docker, Docker Compose
* Orchestration: Kubernetes (Minikube)
* Networking: Nginx Ingress Controller
* Monitoring: Kubernetes Metrics Server

---

## 🔧 Prerequisites

Ensure the following tools are installed:

* Docker Desktop
* Minikube
* kubectl
* Git

---

## 🐳 Phase 1: Local Development (Docker)

### 1️⃣ Clone the Repository

```bash
git clone https://github.com/HarjotSingh2k19/CloudVoyage.git
cd CloudVoyage
```

### 2️⃣ Run with Docker Compose

```bash
docker-compose up --build
```

### 3️⃣ Access the App

* Frontend UI: [http://localhost:3000](http://localhost:3000)
* Backend API: [http://localhost:3500](http://localhost:3500)

Stop containers with `Ctrl + C` or:

```bash
docker-compose down
```

---

## ☸️ Phase 2: Production Deployment (Kubernetes)

### 1️⃣ Start Cluster & Enable Addons

```bash
minikube start
minikube addons enable ingress
minikube addons enable metrics-server
```

### 2️⃣ Deploy Database Layer

```bash
kubectl apply -f k8s/database/
```

(Wait ~30 seconds for MongoDB pod to be ready)

### 3️⃣ Deploy Application Layer

```bash
kubectl apply -f k8s/backend/
kubectl apply -f k8s/frontend/
```

### 4️⃣ Configure Ingress

```bash
kubectl apply -f k8s/ingress.yaml
```

### 5️⃣ 🌐 Network Bridge (Critical)

Start tunnel:

```bash
sudo minikube tunnel
```

Update `/etc/hosts`:

```text
127.0.0.1 wanderlust.local
```

Access the app:

👉 [http://wanderlust.local](http://wanderlust.local)

---

## 🧪 Advanced Verification

### 📈 Test Auto-Scaling (HPA)

Monitor scaling:

```bash
kubectl get hpa -n cloudvoyage -w
```

Generate load:

```bash
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -n cloudvoyage -- /bin/sh -c "while true; do wget -q -O- http://backend-service:3500/ok; done"
```

✅ Backend replicas should scale up automatically.

---

### 🕒 Test Automated Backups

Trigger CronJob manually:

```bash
kubectl create job --from=cronjob/mongo-backup manual-test -n cloudvoyage
kubectl logs -n cloudvoyage job/manual-test
```

Expected output:

```text
Backup Complete!
```

---

## 📂 Project Structure

```text
CloudVoyage/
├── backend/                 # Node.js source & Dockerfile
├── frontend/                # React source & Dockerfile
├── docker-compose.yml       # Local development config
├── k8s/
│   ├── database/            # MongoDB StatefulSet, PVC, Secrets, CronJob
│   ├── backend/             # Deployment, Service, ConfigMap, HPA
│   ├── frontend/            # Deployment, Service
│   └── ingress.yaml         # Ingress rules
└── README.md
```

---

## 🤝 Contributing

Contributions are welcome! Fork the repository and submit a pull request.

---

## 👨‍💻 Author

Harjot Singh
GitHub: [https://github.com/HarjotSingh2k19](https://github.com/HarjotSingh2k19)


