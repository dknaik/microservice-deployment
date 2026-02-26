🚀 Go Microservice + Frontend Deployment (Docker Swarm)
📌 Project Overview

This project demonstrates:

Go backend microservice

Frontend application

Dockerized services

Deployment using Docker Swarm

Reverse proxy architecture

Scalable production-style setup

The goal is to understand end-to-end microservice deployment.

🏗 Architecture
Browser
↓
Reverse Proxy (Caddy / Nginx)
↓
Frontend Service
↓
Backend Service (Go API)

Only reverse proxy is exposed to the internet

Backend runs internally inside Docker network

Services communicate using Docker service names

🧱 Project Structure
project-root/
│
├── backend/
│ ├── main.go
│ ├── go.mod
│ └── Dockerfile
│
├── frontend/
│ ├── src/
│ ├── package.json
│ └── Dockerfile
│
├── swarm.yml
└── README.md
⚙️ Step 1 – Run Locally (Without Docker)
Start Backend
cd backend
go run main.go

Backend runs on:

http://localhost:8080
Start Frontend
cd frontend
npm install
npm start

Frontend calls backend API.

🐳 Step 2 – Dockerize Backend

Build image:

docker build -t yourdockerhub/backend:v1 ./backend

Run container:

docker run -p 8080:8080 yourdockerhub/backend:v1
🐳 Step 3 – Dockerize Frontend

Build image:

docker build -t yourdockerhub/frontend:v1 ./frontend

Run container:

docker run -p 80:80 yourdockerhub/frontend:v1
🌍 Step 4 – Push Images to Docker Hub
docker login
docker push yourdockerhub/backend:v1
docker push yourdockerhub/frontend:v1
🐝 Step 5 – Initialize Docker Swarm
docker swarm init

To add workers:

docker swarm join --token <token> <manager-ip>:2377
📦 Step 6 – Deploy Using Swarm

Deploy stack:

docker stack deploy -c swarm.yml myapp

Check services:

docker service ls

Scale backend:

docker service scale myapp_backend=3
🌐 Service Communication

Inside Docker Swarm:

Services communicate using service names

Example:

http://backend:8080

Frontend must NOT use localhost.

🔁 Updating Service Version

Build new image:

docker build -t yourdockerhub/backend:v2 ./backend

Push:

docker push yourdockerhub/backend:v2

Update in swarm.yml:

image: yourdockerhub/backend:v2

Redeploy:

docker stack deploy -c swarm.yml myapp

Swarm performs rolling update automatically.

📈 Scaling Services

Increase replicas:

docker service scale myapp_backend=5

Swarm distributes containers across nodes.

☁️ Deploying to Cloud

Create server (AWS / DigitalOcean)

Install Docker

Initialize swarm

Deploy stack

Open port 80 in firewall

Application is now live.
