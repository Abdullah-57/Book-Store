# Docker Comprehensive Cheat Sheet for Software Construction and Development

## 1. Introduction to Docker
- **What is Docker?**: Docker is an open-source platform that uses **containerization** to package applications with their dependencies (libraries, configs) into portable **containers**. These containers run consistently across various environments (dev, test, prod) using a shared host OS kernel.
- **Analogy**: Imagine Docker as a standardized shipping container for your app. Just as a shipping container holds goods and can be moved anywhere without repacking, a Docker container holds your app and runs reliably on any system with Docker installed.

## 2. Core Concepts of Docker
### Containers
- **Definition**: Lightweight, standalone units that include an app, its dependencies, and runtime. Containers share the host OS kernel but are isolated with their own filesystem and processes.
- **Use**: Ensures consistency and portability across environments.
- **Scenario**: Deploy a web server on your laptop and then on a production server without changes.
- **Example**: `docker run -d nginx` → Runs an Nginx web server in detached mode at 08:35 PM PKT.

### Images
- **Definition**: Read-only templates used to create containers, built from a `Dockerfile` or pulled from a registry (e.g., Docker Hub).
- **Use**: Serves as the blueprint for containers.
- **Scenario**: Share a pre-built Node.js app image with your team.
- **Example**: `docker pull node:14` → Downloads the Node.js 14 image.

### Dockerfile
- **Definition**: A script with instructions (`FROM`, `RUN`, `COPY`, etc.) to build a Docker image.
- **Use**: Automates the creation of consistent images.
- **Scenario**: Build a custom Python app image.
- **Example** (from provided):
  ```
  FROM ubuntu:20.04
  LABEL maintainer="you@example.com"
  RUN apt update && apt install ...
  WORKDIR /app
  COPY . /app
  ADD http://... /app
  ENV NAME=Ali
  ARG version=1.0
  EXPOSE 3000
  CMD ["npm", "start"]
  ENTRYPOINT ["python3", "main.py"]
  ```
  - Build: `docker build -t my-app .`

### Layers
- **Definition**: Docker images are composed of multiple read-only layers, each created by a Dockerfile instruction. Layers are cached for efficiency.
- **Use**: Optimizes build times by reusing unchanged layers.
- **Scenario**: Modify a single file and rebuild—only affected layers update.
- **Example**: `docker history my-app` → Shows layers like `FROM ubuntu:20.04`, `COPY . /app`.

### Volumes
- **Definition**: Mechanisms to persist data outside the container’s writable layer. Includes **bind mounts** (host-to-container mapping) and **named volumes** (Docker-managed storage).
- **Use**: Ensures data survives container deletion.
- **Scenario**: Store a database’s data persistently.
- **Example**: `docker run -v mydata:/app/data my-app` → Uses a named volume.

### Networks
- **Definition**: Enable communication between containers and the host. Types include **bridge** (default), **host** (no isolation), and **none** (isolated).
- **Use**: Manages container connectivity.
- **Scenario**: Connect a web app to a database container.
- **Example**: `docker network create my_bridge && docker run --network=my_bridge my-app`.

### Docker Compose
- **Definition**: A tool to define and run multi-container apps via a `docker-compose.yml` file.
- **Use**: Simplifies managing complex applications with multiple services.
- **Scenario**: Run a web server with a MySQL database.
- **Example** (from provided):
  ```
  version: '3'
  services:
    web:
      image: nginx
      ports:
        - "80:80"
    db:
      image: mysql
      environment:
        MYSQL_ROOT_PASSWORD: example
  ```
  - Run: `docker-compose up -d`.

### Docker Registry
- **Definition**: A storage and distribution system for Docker images (e.g., Docker Hub, private registries).
- **Use**: Shares images publicly or privately.
- **Scenario**: Publish your app image to Docker Hub for team use.
- **Example**: `docker push mydockerhubuser/myapp:1.0`.

### Docker Swarm
- **Definition**: Docker’s orchestration tool for managing a cluster of nodes to deploy and scale containers.
- **Use**: Handles multi-host container deployment.
- **Scenario**: Scale a web app across multiple servers.
- **Example**: `docker swarm init && docker service create --replicas 3 nginx`.

## 3. Docker Commands and Their Uses
### 1. Docker Installation Check
- `docker --version`
  - **Use**: Verifies Docker installation and version.
  - **Example**: `docker --version` → `Docker version 20.10.7, build f0df350`.
- `docker info`
  - **Use**: Displays system-wide Docker info (e.g., storage, networks).
  - **Example**: `docker info` → Shows runtime details at 08:35 PM PKT.

### 2. Docker Images
- `docker build -t name:tag .`
  - **Use**: Builds an image from a Dockerfile in the current directory.
  - **Scenario**: Create a custom app image.
  - **Example**: `docker build -t myapp:1.0 .`
- `docker images`
  - **Use**: Lists all local images.
  - **Example**: `docker images` → Shows `myapp:1.0`.
- `docker rmi image_id_or_name`
  - **Use**: Removes an image.
  - **Example**: `docker rmi myapp:1.0`
- `docker tag old_name new_name:tag`
  - **Use**: Tags an image for identification or pushing.
  - **Example**: `docker tag myapp:1.0 mydockerhubuser/myapp:1.0`
- `docker pull image_name`
  - **Use**: Downloads an image from a registry.
  - **Example**: `docker pull nginx`
- `docker push image_name`
  - **Use**: Uploads an image to a registry (after login).
  - **Example**: `docker push mydockerhubuser/myapp:1.0`

### 3. Docker Containers
- `docker run image_name`
  - **Use**: Starts a new container from an image.
  - **Example**: `docker run nginx`
- `docker run -it image_name`
  - **Use**: Runs with an interactive terminal.
  - **Example**: `docker run -it ubuntu bash`
- `docker run -d image_name`
  - **Use**: Runs in detached (background) mode.
  - **Example**: `docker run -d nginx`
- `docker run --name container_name image`
  - **Use**: Assigns a name to the container.
  - **Example**: `docker run --name webserver nginx`
- `docker start container_name_or_id`
  - **Use**: Starts a stopped container.
  - **Example**: `docker start webserver`
- `docker stop container_name_or_id`
  - **Use**: Stops a running container.
  - **Example**: `docker stop webserver`
- `docker restart container_name_or_id`
  - **Use**: Restarts a container.
  - **Example**: `docker restart webserver`
- `docker exec -it container_name bash`
  - **Use**: Executes a command (e.g., shell) in a running container.
  - **Example**: `docker exec -it webserver bash`
- `docker ps`
  - **Use**: Lists running containers.
  - **Example**: `docker ps` → Shows `webserver`.
- `docker ps -a`
  - **Use**: Lists all containers (running and stopped).
  - **Example**: `docker ps -a`
- `docker rm container_id`
  - **Use**: Removes a container (stop it first if running).
  - **Example**: `docker rm webserver`
- `docker logs container_name_or_id`
  - **Use**: Views container logs.
  - **Example**: `docker logs webserver`

### 4. Dockerfile Commands Reference
- See detailed explanations in the provided content (e.g., `FROM`, `RUN`, `COPY`, `ADD`, `ENV`, `ARG`, `EXPOSE`, `CMD`, `ENTRYPOINT`).
- **Scenario**: Build a multi-step app image with environment variables and ports.

### 5. Persistent Data
- **Bind Mount**:
  - `docker run -v /host/path:/container/path image_name`
  - **Use**: Maps a host directory to a container.
  - **Scenario**: Edit files on the host and see changes in the container.
  - **Example**: `docker run -v /home/user/data:/app/data my-app`
- **Volumes**:
  - `docker volume create my_volume`
    - **Use**: Creates a named volume.
    - **Example**: `docker volume create my_volume`
  - `docker volume ls`
    - **Use**: Lists volumes.
    - **Example**: `docker volume ls`
  - `docker volume inspect my_volume`
    - **Use**: Shows volume details.
    - **Example**: `docker volume inspect my_volume`
  - `docker run -v my_volume:/container/path image`
    - **Use**: Uses a volume in a container.
    - **Example**: `docker run -v my_volume:/app/data my-app`
  - `docker volume rm my_volume`
    - **Use**: Removes a volume (if unused).
    - **Example**: `docker volume rm my_volume`

### 6. Docker Networks
- `docker network ls`
  - **Use**: Lists all networks.
  - **Example**: `docker network ls`
- `docker network create my_bridge`
  - **Use**: Creates a custom bridge network.
  - **Example**: `docker network create my_bridge`
- `docker network inspect my_bridge`
  - **Use**: Views network details.
  - **Example**: `docker network inspect my_bridge`
- `docker run --network=my_bridge image_name`
  - **Use**: Connects a container to a custom network.
  - **Example**: `docker run --network=my_bridge my-app`
- **Network Types**:
  - **Bridge**: Default, private network for container communication.
  - **Host**: Uses host network (no isolation).
  - **None**: Completely isolated network.
  - **Scenario**: Use `bridge` for a web app and database to communicate.

### 7. Docker Registry
- `docker login`
  - **Use**: Logs into Docker Hub or a private registry.
  - **Example**: `docker login`
- `docker tag local_image username/repo_name:tag`
  - **Use**: Tags an image for pushing.
  - **Example**: `docker tag myapp:1.0 mydockerhubuser/myapp:1.0`
- `docker push username/repo_name:tag`
  - **Use**: Pushes an image to the registry.
  - **Example**: `docker push mydockerhubuser/myapp:1.0`
- `docker pull username/repo_name:tag`
  - **Use**: Pulls an image from the registry.
  - **Example**: `docker pull mydockerhubuser/myapp:1.0`

### 8. Docker Compose
- **Example `docker-compose.yml`**:
  ```
  version: '3'
  services:
    web:
      image: nginx
      ports:
        - "80:80"
    db:
      image: mysql
      environment:
        MYSQL_ROOT_PASSWORD: example
  ```
- `docker-compose up`
  - **Use**: Starts all services.
  - **Example**: `docker-compose up`
- `docker-compose up -d`
  - **Use**: Starts in detached mode.
  - **Example**: `docker-compose up -d`
- `docker-compose down`
  - **Use**: Stops and removes containers.
  - **Example**: `docker-compose down`
- `docker-compose build`
  - **Use**: Builds images defined in the file.
  - **Example**: `docker-compose build`
- `docker-compose ps`
  - **Use**: Lists running containers from the compose file.
  - **Example**: `docker-compose ps`

### 9. Cleanup Commands
- `docker system prune`
  - **Use**: Removes unused containers, images, and networks.
  - **Example**: `docker system prune`
- `docker image prune`
  - **Use**: Removes unused images.
  - **Example**: `docker image prune`
- `docker container prune`
  - **Use**: Removes stopped containers.
  - **Example**: `docker container prune`
- `docker volume prune`
  - **Use**: Removes unused volumes.
  - **Example**: `docker volume prune`

### 10. Misc Useful Commands
- `docker inspect container_or_image`
  - **Use**: Provides low-level details in JSON format.
  - **Example**: `docker inspect webserver`
- `docker stats`
  - **Use**: Shows real-time container resource usage.
  - **Example**: `docker stats`
- `docker top container_name`
  - **Use**: Lists processes in a container.
  - **Example**: `docker top webserver`
- `docker diff container_name`
  - **Use**: Shows file changes in a container.
  - **Example**: `docker diff webserver`

### Bonus: Most Common Use-Case Commands
- **Build Image**:
  - `docker build -t myapp:1.0 .`
  - **Scenario**: Build a custom app image at 08:35 PM PKT.
- **Run with Volume, Port, and Name**:
  - `docker run -d -p 8080:80 -v mydata:/app/data --name webserver myapp:1.0`
  - **Scenario**: Deploy a named web server with persistent data.
- **Stop and Remove Container**:
  - `docker stop webserver`
  - `docker rm webserver`
  - **Scenario**: Clean up after testing.
- **Push to Docker Hub**:
  - `docker tag myapp:1.0 mydockerhubuser/myapp:1.0`
  - `docker push mydockerhubuser/myapp:1.0`
  - **Scenario**: Share your app with the team.

## 4. Example Scenarios
1. **Set Up a Web Server**:
   - Pull: `docker pull nginx`
   - Run: `docker run -d -p 80:80 --name web nginx`
   - Check: `docker ps`
   - Scenario: Host a website at `http://localhost` at 08:35 PM PKT.

2. **Build and Deploy a Custom App**:
   - Dockerfile: Use the provided example.
   - Build: `docker build -t my-app:1.0 .`
   - Run: `docker run -d -p 3000:3000 my-app:1.0`
   - Scenario: Test a Python app at `http://localhost:3000`.

3. **Use Compose for Multi-Service App**:
   - `docker-compose.yml`: Use the provided example.
   - Run: `docker-compose up -d`
   - Scenario: Deploy a web server with MySQL at 08:35 PM PKT.

4. **Clean Up Environment**:
   - Prune: `docker system prune`
   - Scenario: Free up space after testing multiple containers.

## 5. Tips for Exam Preparation
- **Objective Questions**: Memorize commands (e.g., `docker run`, `docker build`, `docker-compose up`).
- **Subjective Questions**: Explain concepts (e.g., “What are Docker volumes?” → Use bind mounts and named volumes).
- **Practice**: Install Docker, build an image, run containers, and use Compose to master the workflow.