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