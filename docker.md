# Hướng dẫn toàn diện về Docker

## Mục lục
1. [Giới thiệu về Docker](#1-giới-thiệu-về-docker)
2. [Kiến trúc Docker](#2-kiến-trúc-docker)
3. [Cài đặt Docker](#3-cài-đặt-docker)
4. [Docker Images](#4-docker-images)
5. [Docker Containers](#5-docker-containers)
6. [Docker Networks](#6-docker-networks)
7. [Docker Volumes](#7-docker-volumes)
8. [Dockerfile](#8-dockerfile)
9. [Docker Compose](#9-docker-compose)
10. [Docker Registry](#10-docker-registry)
11. [Docker Swarm](#11-docker-swarm)
12. [Best Practices](#12-best-practices)
13. [Troubleshooting](#13-troubleshooting)

## 1. Giới thiệu về Docker

### 1.1 Docker là gì?
- Docker là một nền tảng mở để phát triển, vận chuyển và chạy ứng dụng
- Cho phép tách ứng dụng khỏi cơ sở hạ tầng
- Giúp phân phối phần mềm nhanh chóng

### 1.2 Lợi ích của Docker
- Nhẹ và nhanh hơn máy ảo
- Tính di động cao
- Dễ dàng mở rộng
- Tính cách ly
- Tái sử dụng và chia sẻ

## 2. Kiến trúc Docker

### 2.1 Các thành phần chính
```
- Docker Engine
    ├── Docker Daemon (dockerd)
    ├── REST API
    └── Docker CLI (docker)

- Docker Objects
    ├── Images
    ├── Containers
    ├── Networks
    └── Volumes
```

### 2.2 Docker Engine
- Docker Daemon: Quản lý objects Docker
- REST API: Giao tiếp với Docker Daemon
- Docker CLI: Giao diện dòng lệnh

## 3. Cài đặt Docker

### 3.1 Cài đặt trên Ubuntu
```bash
# Cập nhật package index
sudo apt-get update

# Cài đặt các gói cần thiết
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Thêm Docker GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Thiết lập repository
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Cài đặt Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

### 3.2 Kiểm tra cài đặt
```bash
# Kiểm tra phiên bản
docker --version

# Chạy container test
docker run hello-world
```

## 4. Docker Images

### 4.1 Quản lý Images
```bash
# Liệt kê images
docker images
docker image ls

# Tải về image
docker pull image_name:tag

# Xóa image
docker image rm image_name
docker rmi image_name

# Tìm kiếm image
docker search image_name

# Lưu image ra file
docker save -o image.tar image_name

# Nạp image từ file
docker load -i image.tar
```

### 4.2 Tạo Image
```bash
# Từ Dockerfile
docker build -t image_name:tag .

# Từ container
docker commit container_name new_image_name
```

## 5. Docker Containers

### 5.1 Lifecycle của Container
```bash
# Tạo và chạy container
docker run [options] image_name

# Options phổ biến:
-d          # Chạy ở background
-it         # Interactive terminal
--name      # Đặt tên container
-p          # Map port
-v          # Mount volume
--network   # Chỉ định network
-e          # Environment variables
```

### 5.2 Quản lý Containers
```bash
# Xem containers
docker ps
docker ps -a

# Start/Stop/Restart
docker start container_name
docker stop container_name
docker restart container_name

# Xóa container
docker rm container_name
docker rm -f container_name

# Logs
docker logs container_name
docker logs -f container_name

# Execute command
docker exec -it container_name command
```

## 6. Docker Networks

### 6.1 Network Drivers
- bridge: Default network driver
- host: Sử dụng host network
- none: Disabled network
- overlay: Swarm mode network
- macvlan: Assign MAC address

### 6.2 Network Commands
```bash
# Tạo network
docker network create network_name

# Liệt kê networks
docker network ls

# Kết nối container với network
docker network connect network_name container_name

# Ngắt kết nối
docker network disconnect network_name container_name

# Xem chi tiết network
docker network inspect network_name
```

## 7. Docker Volumes

### 7.1 Types of Volumes
- Named volumes
- Bind mounts
- tmpfs mounts

### 7.2 Volume Commands
```bash
# Tạo volume
docker volume create volume_name

# Liệt kê volumes
docker volume ls

# Xem chi tiết volume
docker volume inspect volume_name

# Xóa volume
docker volume rm volume_name

# Sử dụng volume
docker run -v volume_name:/container/path image_name
```

## 8. Dockerfile

### 8.1 Cấu trúc Dockerfile
```dockerfile
# Base image
FROM ubuntu:20.04

# Set working directory
WORKDIR /app

# Copy files
COPY . .

# Run commands
RUN apt-get update && \
    apt-get install -y python3

# Environment variables
ENV PORT=3000

# Expose ports
EXPOSE $PORT

# Default command
CMD ["python3", "app.py"]
```

### 8.2 Multi-stage Builds
```dockerfile
# Build stage
FROM node:14 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
```

## 9. Docker Compose

### 9.1 docker-compose.yml
```yaml
version: '3'

services:
  web:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
    environment:
      - NODE_ENV=development
    depends_on:
      - db
  
  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

### 9.2 Docker Compose Commands
```bash
# Start services
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs

# Scale services
docker-compose up -d --scale web=3

# List services
docker-compose ps
```

## 10. Docker Registry

### 10.1 Docker Hub
```bash
# Login
docker login

# Push image
docker push username/image_name

# Pull image
docker pull username/image_name
```

### 10.2 Private Registry
```bash
# Run private registry
docker run -d -p 5000:5000 registry

# Push to private registry
docker tag image localhost:5000/image
docker push localhost:5000/image
```

## 11. Docker Swarm

### 11.1 Swarm Mode
```bash
# Initialize swarm
docker swarm init

# Join swarm
docker swarm join --token TOKEN IP:PORT

# List nodes
docker node ls

# Deploy service
docker service create --name web nginx

# Scale service
docker service scale web=5
```

## 12. Best Practices

### 12.1 Security
- Use official images
- Scan for vulnerabilities
- Limit container resources
- Don't run as root
- Use secrets management

### 12.2 Performance
- Use multi-stage builds
- Minimize layers
- Use .dockerignore
- Cache efficiently
- Clean up unused objects

## 13. Troubleshooting

### 13.1 Debug Commands
```bash
# Check container stats
docker stats

# View processes
docker top container_name

# Inspect objects
docker inspect container_name

# View events
docker events

# System info
docker info
```

### 13.2 Common Issues
- Container not starting
- Network connectivity issues
- Volume permissions
- Resource constraints
- Image pull failures

### 13.3 Maintenance
```bash
# Clean up system
docker system prune

# Remove unused volumes
docker volume prune

# Remove unused networks
docker network prune

# Check disk usage
docker system df
```
