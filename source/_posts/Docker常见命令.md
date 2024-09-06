---
title: Docker常见命令
tags: docker
abbrlink: a70288e8
date: 2024-09-06 10:36:57
---

Docker 是一个强大的容器化平台，提供了许多命令来管理容器、镜像、网络和存储等。以下是一些常用的 Docker 命令，分为几个类别：

### 1. **镜像管理相关命令**

- **列出本地镜像**
  ```bash
  docker images
  ```
  列出本地 Docker 镜像。

- **拉取镜像**
  ```bash
  docker pull <image_name>
  ```
  从 Docker Hub 或其他镜像仓库拉取镜像。

- **删除镜像**
  ```bash
  docker rmi <image_id>
  ```
  删除本地镜像。

- **构建镜像**
  ```bash
  docker build -t <image_name>:<tag> <path_to_dockerfile>
  ```
  根据 `Dockerfile` 构建新的镜像。

- **查看镜像历史**
  ```bash
  docker history <image_name>
  ```
  查看镜像的历史层信息。

### 2. **容器管理相关命令**

- **运行容器**
  ```bash
  docker run -d -p <host_port>:<container_port> --name <container_name> <image_name>
  ```
  运行一个新的容器，`-d` 表示后台运行，`-p` 映射端口，`--name` 给容器命名。

- **列出运行中的容器**
  ```bash
  docker ps
  ```
  查看当前正在运行的容器。

- **列出所有容器**
  ```bash
  docker ps -a
  ```
  列出所有容器，包括已经停止的容器。

- **停止容器**
  ```bash
  docker stop <container_name_or_id>
  ```
  停止正在运行的容器。

- **启动已停止的容器**
  ```bash
  docker start <container_name_or_id>
  ```
  启动一个已停止的容器。

- **删除容器**
  ```bash
  docker rm <container_name_or_id>
  ```
  删除已停止的容器。

- **进入运行中的容器**
  ```bash
  docker exec -it <container_name_or_id> /bin/bash
  ```
  进入容器的终端，可以在运行的容器内执行命令。

### 3. **日志和监控相关命令**

- **查看容器日志**
  ```bash
  docker logs <container_name_or_id>
  ```
  查看容器的日志输出。

- **实时查看容器日志**
  ```bash
  docker logs -f <container_name_or_id>
  ```
  实时跟踪容器的日志。

- **查看容器资源使用情况**
  ```bash
  docker stats
  ```
  实时查看容器的 CPU、内存、网络和 I/O 使用情况。

### 4. **网络管理相关命令**

- **列出 Docker 网络**
  ```bash
  docker network ls
  ```
  列出 Docker 中的所有网络。

- **创建 Docker 网络**
  ```bash
  docker network create <network_name>
  ```
  创建一个新的自定义网络。

- **将容器连接到网络**
  ```bash
  docker network connect <network_name> <container_name_or_id>
  ```
  将一个容器连接到一个网络。

- **断开容器和网络**
  ```bash
  docker network disconnect <network_name> <container_name_or_id>
  ```
  从网络中断开容器。

### 5. **卷管理相关命令**

- **列出 Docker 卷**
  ```bash
  docker volume ls
  ```
  列出所有 Docker 卷。

- **创建卷**
  ```bash
  docker volume create <volume_name>
  ```
  创建新的 Docker 卷。

- **挂载卷到容器**
  ```bash
  docker run -v <volume_name>:/path/in/container <image_name>
  ```
  在启动容器时挂载一个卷到容器内的指定目录。

- **删除卷**
  ```bash
  docker volume rm <volume_name>
  ```
  删除指定的 Docker 卷。

### 6. **清理相关命令**

- **删除未使用的容器、网络、镜像和卷**
  ```bash
  docker system prune
  ```
  清理未使用的容器、网络、镜像和卷。

- **删除所有未使用的卷**
  ```bash
  docker volume prune
  ```

### 7. **容器导入/导出相关命令**

- **导出容器**
  ```bash
  docker export <container_name_or_id> > /path/to/file.tar
  ```
  将容器导出为 tar 文件。

- **导入容器**
  ```bash
  docker import /path/to/file.tar
  ```
  将导出的 tar 文件导入为 Docker 镜像。

### 8. **Docker Compose 相关命令**
Docker Compose 是一个定义和运行多容器 Docker 应用程序的工具。

- **启动服务**
  ```bash
  docker-compose up
  ```

- **后台启动服务**
  ```bash
  docker-compose up -d
  ```

- **停止服务**
  ```bash
  docker-compose down
  ```

这些命令涵盖了 Docker 的核心功能，可以帮助你在日常使用中更高效地管理容器、镜像和网络等。
