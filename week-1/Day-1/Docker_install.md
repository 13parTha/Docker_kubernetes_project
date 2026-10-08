Docker installed on Windows WSL setup. Here Ubuntu version is 26.04.1 TLS.
Here are the commands to install docker , docker cli, docker compose & other required packages.
Commamnd are - 
1. Removing old or conflicting Packages.
    for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do
        sudo apt remove $pkg
    done
2. Install Dependencies -
    sudo apt update
    sudo apt install -y ca-certificates curl gnupg lsb-release
3. Add Docker's GPG key
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
        -o /etc/apt/keyrings/docker.asc

4. Add Docker repository
    echo \
        "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
        https://download.docker.com/linux/ubuntu \
        $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
        sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

5. Install docker Engine
    sudo apt update
    sudo apt install -y docker-ce docker-ce-cli containerd.io \
        docker-buildx-plugin docker-compose-plugin

6. Post Installation Steps
    sudo systemctl enable docker
    sudo systemctl start docker
    sudo systemctl status docker

7. Run docker without sudo setup
    sudo usermod -aG docker $USER
    newgrp docker

8. Verify Installation
    docker --version
    docker compose version
    docker run hello-world 

---

## Minikube, kubectl & Helm Installation on Ubuntu (WSL)

### Minikube Installation

1. Install conntrack (required dependency)
    sudo apt-get install -y conntrack

2. Download Minikube binary
    curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

3. Install Minikube
    sudo install minikube-linux-amd64 /usr/local/bin/minikube

4. Clean up downloaded file
    rm minikube-linux-amd64

5. Verify Minikube installation
    minikube version

---

### kubectl Installation

6. Download kubectl (latest stable release)
    curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

7. Install kubectl
    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

8. Clean up downloaded file
    rm kubectl

9. Verify kubectl installation
    kubectl version --client

---

### Start Minikube & Verify Cluster

10. Start Minikube with Docker driver
    minikube start --driver=docker

11. Check Minikube status
    minikube status

12. Verify nodes
    kubectl get nodes

13. View cluster info
    kubectl cluster-info

14. List nodes again (confirm Ready state)
    kubectl get nodes

---

### Helm Installation

15. Install Helm using the official script
    curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

16. Verify Helm installation
    helm version