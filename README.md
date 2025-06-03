# 🌈 Color API: Node.js + Docker + Kubernetes

Welcome to the **Color API**! A simple Node.js app that returns a blue-colored greeting, built with Express, containerized with Docker, and deployed using Kubernetes.

Perfect for practicing **Docker image building**, **Node.js app setup**, and **Kubernetes deployment** from scratch. 🚀

---

## 📌 What You'll Learn

✅ Build a basic Express.js app  
✅ Containerize it using Docker  
✅ Push it to Docker Hub  
✅ Deploy it to Kubernetes  
✅ Test internal pod communication  
✅ Clean up everything properly

---

## ⚙️ Prerequisites

- ✅ Node.js installed → [Download Here](https://nodejs.org/en/download)  
- ✅ Docker installed and running  
- ✅ Kubernetes cluster (Minikube, kind, etc.)  
- ✅ Docker Hub account

---

## 🧾 One-Click Copy Setup Guide

> 📋 Copy and paste the **entire code block below** into your terminal to get started!

<details>
<summary>📋 Click to Expand & Copy Full Setup (all steps included)</summary>

<br/>

```bash
# 🏗️ Step 1: Create Project Structure
mkdir color-api && cd color-api
mkdir src
touch src/index.js Dockerfile .dockerignore package.json

# 📦 Step 2: Initialize Node.js & Install Express
npm init -y
# If you see an error: rm package.json && npm init -y
npm install express --save-exact

# 🧠 Step 3: Create Express App
cat <<EOF > src/index.js
const express = require("express");
const app = express();
const port = 80;

app.get("/", (req, res) => {
  res.send("<h1 style='color:blue;'>Hello from Color API</h1>");
});

app.listen(port, () => {
  console.log(\`Color API listening on port \${port}\`);
});
EOF

# 🐳 Step 4: Create Dockerfile
cat <<EOF > Dockerfile
FROM node:22-alpine3.20
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY src ./src
CMD ["node", "src/index.js"]
EOF

# 🚫 Step 5: Add .dockerignore
echo -e "node_modules\nnpm-debug.log" > .dockerignore

# 🛠️ Step 6: Build & Push Docker Image (replace with your Docker Hub username)
docker build -t your-dockerhub-username/color-api:1.0.0 .
docker login
docker push your-dockerhub-username/color-api:1.0.0

# ☸️ Step 7: Deploy to Kubernetes
kubectl run color-api --image=your-dockerhub-username/color-api:1.0.0
kubectl get pods
kubectl logs color-api
kubectl describe pod color-api | grep IP

# 🌐 Step 8: Test Connectivity from Another Pod
kubectl run alpine --image=alpine:3.20 -it -- sh
# Inside the Alpine pod:
apk add curl
curl <REPLACE_WITH_COLOR_API_POD_IP>

# 🧹 Step 9: Clean Up
kubectl delete pod alpine --force --grace-period=0
kubectl delete pod color-api --force --grace-period=0
