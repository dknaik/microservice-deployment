🚀 Simple Go + Frontend App (Docker Swarm + Caddy)
📌 Overview

This project demonstrates:

Go backend microservice

Simple frontend (HTML + JS)

Docker containerization

Docker Swarm deployment

Reverse proxy using Caddy

Service-to-service communication

Horizontal scaling

The goal is to understand end-to-end microservice deployment in a production-style setup.

🏗 Architecture
Browser
↓
Caddy (Reverse Proxy)
↓
Frontend Service
↓
Backend Service (Go API)
Key Principles

Only Caddy is exposed to the internet

Backend is NOT publicly exposed

Services communicate using Docker service names

Swarm handles scaling and failover

📂 Project Structure
project-root/
│
├── backend/
│ ├── main.go
│ ├── go.mod
│ └── Dockerfile
│
├── frontend/
│ ├── index.html
│ └── Dockerfile
│
├── Caddyfile
├── swarm.yml
└── README.md
🧱 Backend (Go API)
main.go
package main

import (
"fmt"
"net/http"
)

func greet(w http.ResponseWriter, r \*http.Request) {
fmt.Fprintln(w, "Hello from Go Backend 🚀")
}

func main() {
http.HandleFunc("/api/greet", greet)
http.ListenAndServe(":8080", nil)
}
Backend Dockerfile
FROM golang:1.22-alpine
WORKDIR /app
COPY go.mod .
RUN go mod download
COPY . .
RUN go build -o app
EXPOSE 8080
CMD ["./app"]

Build image:

docker build -t yourdockerhub/backend:v1 ./backend
🌐 Frontend
index.html

<!DOCTYPE html>
<html>
<head>
  <title>Simple App</title>
</head>
<body>
  <h1>Frontend</h1>
  <button onclick="callAPI()">Call Backend</button>
  <p id="result"></p>

  <script>
    function callAPI() {
      fetch("/api/greet")
        .then(res => res.text())
        .then(data => {
          document.getElementById("result").innerText = data;
        });
    }
  </script>
</body>
</html>
Frontend Dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80

Build image:

docker build -t yourdockerhub/frontend:v1 ./frontend
📦 Push Images to Docker Hub
docker login
docker push yourdockerhub/backend:v1
docker push yourdockerhub/frontend:v1
🐝 Initialize Docker Swarm

On your server:

docker swarm init

To add worker nodes:

docker swarm join --token <token> <manager-ip>:2377
🌍 Caddy Reverse Proxy
Caddyfile
:80 {

    handle_path /api/* {
        reverse_proxy backend:8080
    }

    handle {
        reverse_proxy frontend:80
    }

}
What This Does

/api/\* → routed to backend

All other routes → routed to frontend

📦 swarm.yml
version: "3.8"

services:
backend:
image: yourdockerhub/backend:v1
deploy:
replicas: 2
networks: - appnet

frontend:
image: yourdockerhub/frontend:v1
deploy:
replicas: 1
networks: - appnet

caddy:
image: caddy:latest
ports: - "80:80"
volumes: - ./Caddyfile:/etc/caddy/Caddyfile
networks: - appnet

networks:
appnet:
🚀 Deploy the Stack
docker stack deploy -c swarm.yml myapp

Check running services:

docker service ls

Open browser:

http://localhost

Click the button → backend response appears.

📈 Scaling Backend

Scale backend service:

docker service scale myapp_backend=3

Swarm will distribute containers automatically.

🔁 Updating Backend Version

Build new image:

docker build -t yourdockerhub/backend:v2 ./backend

Push image:

docker push yourdockerhub/backend:v2

Update image tag inside swarm.yml

Redeploy:

docker stack deploy -c swarm.yml myapp

Swarm performs rolling updates automatically.
