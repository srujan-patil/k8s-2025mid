# 🌈 Color API with Node.js, Docker & Kubernetes

Welcome to the **Color API** project – a minimal, containerized API built with **Node.js + Express**, Dockerized for deployment, and Kubernetes-ready! 🚀

---

## 📦 Prerequisites

- [Install Node.js](https://nodejs.org/en/download)
- Docker installed and running
- Kubernetes cluster (Minikube, kind, or cloud)
- Docker Hub account

---

## 🧾 One-Click Copy Setup Guide

<details>
<summary>📋 Click to Expand & Copy Entire Setup</summary>

<br/>

```bash
# Step 1: Project Structure
mkdir color-api && cd color-api
mkdir src
touch src/index.js Dockerfile .dockerignore package.json

# Step 2: Initialize Node.js & Install Express
npm init -y
# If error: rm package.json && npm init -y
npm install express --save-exact

# Step 3: Create Express App
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

# Step 4: Dockerfile
cat <<EOF > Dockerfile
FROM node:22-alpine3.20
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY src ./src
CMD ["node", "src/index.js"]
EOF

# Step 5: .dockerignore
echo -e "node_modules\nnpm-debug.log" > .dockerignore

# Step 6: Build & Push Docker Image (replace 'your-dockerhub-username')
docker build -t your-dockerhub-username/color-api:1.0.0 .
docker login
docker push your-dockerhub-username/color-api:1.0.0

# Step 7: Deploy to Kubernetes
kubectl run color-api --image=your-dockerhub-username/color-api:1.0.0
kubectl get pods
kubectl logs color-api
kubectl describe pod color-api | grep IP

# Step 8: Test via Alpine Pod
kubectl run alpine --image=alpine:3.20 -it -- sh
# Inside Alpine shell:
apk add curl
curl <REPLACE_WITH_COLOR_API_POD_IP>

# Step 9: Clean up
kubectl delete pod alpine --force --grace-period=0
kubectl delete pod color-api --force --grace-period=0
