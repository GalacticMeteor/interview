# 🐳 Docker Course for DevOps Engineers (Interview‑Ready)

This course is a **practical + theoretical guide** to Docker, designed for **DevOps interviews and real-world usage**. All commands listed are commonly expected knowledge for a DevOps engineer.

---

## 1️⃣ Docker Setup

### What is Docker?
Docker is a **containerization platform** that allows you to package an application with its dependencies into a **container** that can run consistently across environments.

### Install Docker

#### Linux (Ubuntu)
```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

#### Verify installation
```bash
docker --version
docker info
docker run hello-world
```

---

## 2️⃣ Essential Docker Commands (Must‑Know)

```bash
docker version
docker info
docker help
```

### Containers
```bash
docker ps
docker ps -a
docker start <container>
docker stop <container>
docker restart <container>
docker rm <container>
docker logs <container>
docker exec -it <container> bash
```

### Images
```bash
docker images
docker pull nginx
docker rmi nginx
docker build -t myimage .
```

### System / Cleanup
```bash
docker system df
docker system prune
docker container prune
docker image prune
```

---

## 3️⃣ `docker run` Explained

### Basic syntax
```bash
docker run [OPTIONS] IMAGE [COMMAND]
```

### Common options
```bash
docker run -d nginx           # detached
docker run -it ubuntu bash    # interactive
docker run -p 8080:80 nginx   # port mapping
docker run --name web nginx   # container name
docker run --rm nginx         # auto remove
```

---

## 4️⃣ Docker Images, Environment Variables, CMD vs ENTRYPOINT

### Docker Image
A Docker image is a **read‑only template** used to create containers.

### Environment Variables

#### At runtime
```bash
docker run -e ENV=prod nginx
```

#### Using `.env`
```env
ENV=prod
DB_HOST=localhost
```

```bash
docker run --env-file .env myapp
```

#### In Dockerfile
```dockerfile
ENV APP_ENV=production
```

⚠️ Do NOT store secrets in images.

---

### CMD vs ENTRYPOINT (Very Important 🔥)

| CMD | ENTRYPOINT |
|----|----|
| Default command | Main executable |
| Can be overridden | Hard to override |

```dockerfile
CMD ["nginx", "-g", "daemon off;"]
```

```dockerfile
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
```

---

## 5️⃣ Docker Compose

### What is Docker Compose?
Docker Compose is a tool to **define and run multi‑container applications** using YAML.

### Simple Example

```yaml
version: "3.8"
services:
  web:
    image: nginx
    ports:
      - "8080:80"
```

```bash
docker compose up -d
docker compose down
docker compose ps
docker compose logs
```

---

## 6️⃣ Docker Engine

### What is Docker Engine?
Docker Engine is the **core runtime** that builds and runs containers.

### Components
- **Docker Daemon (`dockerd`)** – runs containers
- **Docker CLI (`docker`)** – user interface
- **REST API** – communication layer

---

## 7️⃣ Docker Storage (Volumes & Bind Mounts)

### Why Storage?
Containers are **ephemeral** → data is lost when removed.

---

### Volumes (Recommended ✅)

```bash
docker volume create mydata
```

```bash
docker run -v mydata:/var/lib/mysql mysql
```

```bash
docker volume ls
docker volume inspect mydata
docker volume rm mydata
```

---

### Bind Mounts

```bash
docker run -v /host/data:/container/data nginx
```

⚠️ Tied to host filesystem.

---

## 8️⃣ Docker Networking

### Default Networks
```bash
docker network ls
```

- bridge (default)
- host
- none

---

### Create Custom Network
```bash
docker network create mynet
```

```bash
docker run --network=mynet --name app1 nginx
docker run --network=mynet --name app2 busybox
```

Containers can communicate using **container names**.

---

## 9️⃣ Docker Registry & Pushing Images

### What is a Docker Registry?
A registry stores Docker images (Docker Hub, AWS ECR, GitHub Registry).

---

### Login
```bash
docker login
```

### Tag Image
```bash
docker tag myapp:1.0 username/myapp:1.0
```

### Push Image
```bash
docker push username/myapp:1.0
```

### Pull Image
```bash
docker pull username/myapp:1.0
```

---

## 🔟 Dockerfile Example – Nginx Application

### Essential Dockerfile Directives

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### Build & Run
```bash
docker build -t my-nginx .
docker run -p 8080:80 my-nginx
```

---

## 📌 Essential Docker Directives (Interview List)

- `FROM`
- `RUN`
- `COPY`
- `ADD`
- `WORKDIR`
- `ENV`
- `ARG`
- `EXPOSE`
- `CMD`
- `ENTRYPOINT`
- `VOLUME'

## 🎯 Interview Questions & Answers (DevOps)

### 1️⃣ CMD vs ENTRYPOINT

**CMD**
- Defines the default command
- Can be overridden at runtime

**ENTRYPOINT**
- Defines the main executable
- Arguments passed via CMD

✅ Best practice:
```dockerfile
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
```

---

### 2️⃣ Containers vs Virtual Machines

| Containers | Virtual Machines |
|---------|----------------|
| Share host OS kernel | Each VM has its own OS |
| Lightweight | Heavy |
| Fast startup | Slow startup |
| Less resource usage | High resource usage |

👉 Containers = process isolation
👉 VMs = hardware virtualization

---

### 3️⃣ Volumes & Networks

#### Volumes
- Persist data beyond container lifecycle
- Managed by Docker

```bash
docker volume create myvol
docker run -v myvol:/data nginx
```

#### Networks
- Allow containers to communicate
- DNS-based service discovery

```bash
docker network create app-net
docker run --network app-net --name api nginx
```

---

### 4️⃣ `docker run` Flags (Must Know)

| Flag | Purpose |
|----|-------|
| `-d` | Detached mode |
| `-it` | Interactive terminal |
| `-p` | Port mapping |
| `-v` | Volume mount |
| `--name` | Container name |
| `--rm` | Auto-remove |
| `-e` | Environment variable |
| `--network` | Attach network |

Example:
```bash
docker run -d --name web -p 8080:80 -v data:/usr/share/nginx/html nginx
```

---

### 5️⃣ Docker Compose Basics

Docker Compose allows you to **define, run, and manage multi-container applications**.

```yaml
services:
  web:
    image: nginx
    ports:
      - "8080:80"
```

Commands:
```bash
docker compose up -d
docker compose down
docker compose ps
docker compose logs
```

👉 Compose is mandatory knowledge for DevOps interviews.

---

## ✅ Final Interview Checklist

✔ CMD vs ENTRYPOINT
✔ Containers vs VMs
✔ Volumes & Networks
✔ docker run flags
✔ Docker Compose fundamentals

You are now **Docker interview-ready** 🚀 (DevOps)

✅ Know **CMD vs ENTRYPOINT**
✅ Explain **containers vs VMs**
✅ Understand **volumes & networks**
✅ Be comfortable with `docker run` flags
✅ Docker Compose basics are mandatory



