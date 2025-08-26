# Installation Docker Engine on Linux

## Installation procedures for supported platforms

![alt text](images/installation/image.png)

## Install Docker Engine on Ubuntu

### Uninstall old versions

Before you can install Docker Engine, you need to uninstall any conflicting packages.

Your Linux distribution may provide unofficial Docker packages, which may conflict with the official packages provided by Docker. You must uninstall these packages before you install the official version of Docker Engine.

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

### Install using the apt repository

1. Set up Docker's apt repository.

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

2. Install the Docker packages.

- Latest

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- Specific Version 

```bash
# List the available versions:
apt-cache madison docker-ce | awk '{ print $3 }'

5:28.3.3-1~ubuntu.24.04~noble
5:28.3.2-1~ubuntu.24.04~noble
...

# Select the desired version and install:
VERSION_STRING=5:28.3.3-1~ubuntu.24.04~noble
sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
```

3. Verify that the installation is successful by running the hello-world image:

```bash
sudo docker run hello-world
```

4. Verify Docker Engine version

```docker
docker ---version
```

5. Run your first container

```bash
docker run -d -p 8080:80 docker/welcome-to-docker
```

For this container, the frontend is accessible on port 8080. To open the website, visit http://`[your_IP]`:8080 in your browser.

![alt text](images/installation/image-1.png)