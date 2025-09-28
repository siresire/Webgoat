

This repository contains instructions for installing and running **OWASP WebGoat** using Docker. The guide is tailored for Debian-based systems (e.g., Kali Linux) but can be adapted for other platforms.

---

## Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Installation](#installation)
   - [Docker installation](#docker-installation)
   - [Pull WebGoat image](#pull-webgoat-image)
   - [Run WebGoat](#run-webgoat)
4. [Docker Compose Example](#docker-compose-example)
5. [Verify & Access WebGoat](#verify--access-webgoat)
6. [Useful Commands](#useful-commands)
7. [Persistence & Configuration](#persistence--configuration)
8. [Troubleshooting](#troubleshooting)
9. [Security Notes](#security-notes)
10. [References](#references)

---

## Overview

[WebGoat](https://owasp.org/www-project-webgoat/) is an intentionally insecure application maintained by OWASP for security training. This guide demonstrates how to set up WebGoat locally using Docker.

---

## Prerequisites

- Debian-based OS (e.g., Kali Linux)
- Internet access
- User with `sudo` privileges
- Basic knowledge of Linux shell

---

## Installation

### Docker installation

```yaml 
# Update system
sudo apt update && sudo apt upgrade -y

# Install dependencies
sudo apt install apt-transport-https ca-certificates curl software-properties-common gnupg -y

# Add Docker’s GPG key
curl -fsSL https://download.docker.com/linux/debian/gpg | \
  sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add Docker repository
echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
  https://download.docker.com/linux/debian $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y

# Start Docker service
sudo systemctl start docker
sudo systemctl enable docker


# Verify installation
docker --version
```

**Optional (run Docker without sudo):**

```yaml
sudo usermod -aG docker $USER
newgrp docker
```

---

### Pull WebGoat image

```yaml
# Search image (optional)
docker search webgoat

# Pull official image
docker pull webgoat/webgoat:latest

# Verify image
docker images | grep webgoat
```

---

### Run WebGoat

```yaml
# Run container
docker run -d -p 8080:8080 --name webgoat webgoat/webgoat:latest

# Check running containers
docker ps -a
```

---

## Docker Compose Example

```yaml
version: '3.8'
services:
  webgoat:
    image: webgoat/webgoat:latest
    container_name: webgoat
    ports:
      - "8080:8080"
    restart: unless-stopped

  webwolf:
    image: webgoat/webwolf:latest
    container_name: webwolf
    ports:
      - "9090:9090"
    restart: unless-stopped
```

Run with:

```yaml
docker compose up -d
```

---

## Verify & Access WebGoat

- Check logs:

```yaml
docker logs -f webgoat
```

- Open in browser:

```yaml
http://localhost:8080/WebGoat
```

- Or test with curl:

```yaml
curl -s http://localhost:8080/WebGoat/login | head -n 20
```

---

## Useful Commands

```yaml
# Stop container
docker stop webgoat

# Start container
docker start webgoat

# Restart container
docker restart webgoat

# Remove container
docker rm -f webgoat

# Remove image
docker rmi webgoat/webgoat:latest

# Inspect container
docker inspect webgoat
```

---

## Persistence & Configuration

By default, data is lost when the container stops. Use a volume for persistence:

```yaml
docker run -d -p 8080:8080 --name webgoat \
  -v $PWD/webgoat-data:/home/webgoat/webgoat \
  webgoat/webgoat:latest
```

---

## Troubleshooting

- **Port in use:** change to `-p 8081:8080`
- **Container unhealthy:** check logs with `docker logs webgoat`
- **Permission errors:** re-login after adding user to `docker` group

---

## Security Notes

- WebGoat is intentionally vulnerable — **never expose it publicly**.
- Use isolated environments or virtual machines.
- Never use real credentials.
---

## References

- [WebGoat Project](https://owasp.org/www-project-webgoat/)
- [Docker Hub – WebGoat](https://hub.docker.com/r/webgoat/webgoat)
- [Docker Documentation](https://docs.docker.com/)

---

