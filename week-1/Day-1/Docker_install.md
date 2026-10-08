# Docker, Minikube, kubectl & Helm Installation on Windows WSL (Ubuntu 26.04.1 LTS)

## Docker Installation

Here are the commands to install Docker, Docker CLI, Docker Compose & other required packages.

### 1. Remove Old or Conflicting Packages

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do
    sudo apt remove $pkg
done
```

### 2. Install Dependencies

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release
```

### 3. Add Docker's GPG Key

```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
    -o /etc/apt/keyrings/docker.asc
```

### 4. Add Docker Repository

```bash
echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
    https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 5. Install Docker Engine

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io \
    docker-buildx-plugin docker-compose-plugin
```

### 6. Post Installation Steps

```bash
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
```

### 7. Run Docker Without Sudo

```bash
sudo usermod -aG docker $USER
newgrp docker
```

### 8. Verify Installation

```bash
docker --version
docker compose version
docker run hello-world
```

---

## Minikube, kubectl & Helm Installation on Ubuntu (WSL)

### Minikube Installation

#### 1. Install conntrack (required dependency)

```bash
sudo apt-get install -y conntrack
```

#### 2. Download Minikube Binary

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

#### 3. Install Minikube

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

#### 4. Clean Up Downloaded File

```bash
rm minikube-linux-amd64
```

#### 5. Verify Minikube Installation

```bash
minikube version
```

---

### kubectl Installation

#### 6. Download kubectl (Latest Stable Release)

```bash
curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

#### 7. Install kubectl

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

#### 8. Clean Up Downloaded File

```bash
rm kubectl
```

#### 9. Verify kubectl Installation

```bash
kubectl version --client
```

---

### Start Minikube & Verify Cluster

#### 10. Start Minikube with Docker Driver

```bash
minikube start --driver=docker
```

#### 11. Check Minikube Status

```bash
minikube status
```

#### 12. Verify Nodes

```bash
kubectl get nodes
```

#### 13. View Cluster Info

```bash
kubectl cluster-info
```

#### 14. List Nodes Again (Confirm Ready State)

```bash
kubectl get nodes
```

---

### Helm Installation

#### 15. Install Helm Using the Official Script

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

#### 16. Verify Helm Installation

```bash
helm version
```
