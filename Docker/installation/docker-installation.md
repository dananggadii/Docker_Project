# Docker Engine Installation on Linux

## Supported Platforms

![Docker Installation](../images/installation/docker/image.png)

---

## Install Docker Engine on Ubuntu

### 1. Uninstall Old Versions

Before installing Docker Engine, remove any old or unofficial Docker packages to avoid conflicts:

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

---

### 2. Install via apt Repository

#### a. Set Up Repository

```bash
# Add Docker's official GPG key
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add Docker repository to Apt sources
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo \"${UBUNTU_CODENAME:-$VERSION_CODENAME}\") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

#### b. Install Docker Packages

- **Latest Version:**

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- **Specific Version:**

```bash
# List available versions:
apt-cache madison docker-ce | awk '{ print $3 }'

# Example output:
# 5:28.3.3-1~ubuntu.24.04~noble
# 5:28.3.2-1~ubuntu.24.04~noble
# ...

# Select the desired version and install:
VERSION_STRING=5:28.3.3-1~ubuntu.24.04~noble
sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
```

---

### 3. Verify Installation

Run the hello-world image to confirm Docker is installed correctly:

```bash
sudo docker run hello-world
```

---

### 4. Check Docker Engine Version

```bash
docker --version
```

---

### 5. Run Your First Container

```bash
docker run -d -p 8080:80 docker/welcome-to-docker
```

The frontend of this container is accessible on port 8080. Open your browser and visit: `http://[your_IP]:8080`

![Welcome Docker](../images/installation/docker/image-1.png)