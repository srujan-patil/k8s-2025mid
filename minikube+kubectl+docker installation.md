# 🐳 Automated Docker + Minikube Setup Script (Ubuntu)

This script automates the installation of **Docker**, **kubectl**, and **Minikube** on Ubuntu-based systems. It supports both **x86_64** and **ARM64 (aarch64)** architectures.

---

## 📋 One-Click Setup Script

<details>
<summary>📜 <strong>Click to Expand Full Script</strong></summary>

```bash
#!/bin/bash

# Detect system architecture
ARCH=$(arch)

echo "🔍 Detected Architecture: $ARCH"

echo "📦 Installing Docker dependencies..."
sudo apt-get update -y
sudo apt-get install -y ca-certificates curl gnupg lsb-release

# Add Docker's official GPG key
echo "🔐 Adding Docker GPG key..."
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Set up Docker's stable repository
echo "🔗 Adding Docker repository..."
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
echo "🐳 Installing Docker Engine..."
sudo apt-get update -y
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Post-install: Add current user to Docker group
echo "👤 Adding user to Docker group..."
sudo usermod -aG docker $USER
newgrp docker

# Architecture-specific installations
if [ $ARCH = "x86_64" ]; then
  echo "💻 x86_64 system detected. Installing kubectl & Minikube..."

  # Install kubectl (Kubernetes CLI)
  curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
  chmod +x ./kubectl
  sudo mv ./kubectl /usr/local/bin/kubectl

  # Install Minikube
  curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  sudo install minikube-linux-amd64 /usr/local/bin/minikube

elif [ $ARCH = "aarch64" ]; then
  echo "📱 ARM64 (aarch64) system detected. Installing ARM-compatible Minikube..."

  # Install Minikube for ARM
  curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-arm64
  sudo install minikube-linux-arm64 /usr/local/bin/minikube

  # Install kubectl via Snap
  sudo snap install kubectl --classic
fi

echo "✅ Setup Complete!"
echo "👉 Now run: minikube start"
