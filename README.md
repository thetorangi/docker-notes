# Docker Complete Guide 🐋

## Table of Contents
- [What is Docker?](#what-is-docker)
- [Why Docker?](#why-docker)
- [Docker vs Virtual Machines](#docker-vs-virtual-machines)
- [Core Docker Concepts](#core-docker-concepts)
- [Docker Architecture](#docker-architecture)
- [Docker Commands Reference](#docker-commands-reference)
  - [Image Commands](#image-commands)
  - [Container Commands](#container-commands)
  - [Docker Hub Commands](#docker-hub-commands)
  - [Volume Commands](#volume-commands)
  - [Network Commands](#network-commands)
  - [Docker Compose Commands](#docker-compose-commands)
  - [System Commands](#system-commands)
  - [Troubleshooting Commands](#troubleshooting-commands)
- [Dockerfile](#dockerfile)
- [Docker Compose](#docker-compose)
- [Best Practices](#best-practices)

---

## What is Docker?

Docker is an open-source platform that enables developers to **automate the deployment** of applications inside lightweight, portable containers. Containers package an application with all its dependencies, libraries, and configuration files, ensuring it runs consistently across different computing environments.

### Key Features:
- **Containerization**: Packages applications with dependencies
- **Portability**: Run anywhere - development, testing, production
- **Isolation**: Each container runs independently
- **Efficiency**: Lightweight compared to virtual machines
- **Scalability**: Easy to scale applications horizontally

---

## Why Docker?

### Problems Docker Solves:

1. **"It works on my machine" problem**
   - Eliminates environment inconsistencies between development, testing, and production

2. **Dependency Management**
   - All dependencies are packaged within the container
   - No conflicts between different application requirements

3. **Resource Efficiency**
   - Containers share the host OS kernel
   - Lower overhead compared to VMs
   - Faster startup times (seconds vs minutes)

4. **Microservices Architecture**
   - Perfect for breaking monolithic applications into smaller services
   - Each service runs in its own container

5. **Continuous Integration/Deployment (CI/CD)**
   - Consistent environments across the pipeline
   - Faster build and deployment cycles

---

## Docker vs Virtual Machines

| Feature | Docker Containers | Virtual Machines |
|---------|------------------|------------------|
| **Architecture** | Share host OS kernel | Each VM has its own OS |
| **Size** | Lightweight (MBs) | Heavy (GBs) |
| **Startup Time** | Seconds | Minutes |
| **Performance** | Near-native | Slower due to hypervisor overhead |
| **Isolation** | Process-level | Complete isolation |
| **Resource Usage** | Minimal | High (RAM, CPU, Storage) |
| **Portability** | Highly portable | Less portable |
| **Use Case** | Microservices, DevOps | Running multiple OS types, legacy apps |

### Visual Representation:

```
Docker Architecture:
┌─────────────────────────────────────┐
│        Application Containers       │
│  ┌─────┐  ┌─────┐  ┌─────┐         │
│  │App 1│  │App 2│  │App 3│         │
│  └─────┘  └─────┘  └─────┘         │
├─────────────────────────────────────┤
│          Docker Engine              │
├─────────────────────────────────────┤
│          Host Operating System      │
├─────────────────────────────────────┤
│          Infrastructure             │
└─────────────────────────────────────┘

VM Architecture:
┌─────────────────────────────────────┐
│         Virtual Machines            │
│  ┌──────────┐  ┌──────────┐        │
│  │  App 1   │  │  App 2   │        │
│  │ Guest OS │  │ Guest OS │        │
│  └──────────┘  └──────────┘        │
├─────────────────────────────────────┤
│          Hypervisor                 │
├─────────────────────────────────────┤
│        Host Operating System        │
├─────────────────────────────────────┤
│          Infrastructure             │
└─────────────────────────────────────┘
```

---

## Core Docker Concepts

### 1. **Docker Image**
- Read-only template containing application code, runtime, libraries, and dependencies
- Built from a Dockerfile
- Images are layered (each instruction creates a new layer)
- Immutable - once created, never changes

### 2. **Docker Container**
- Running instance of a Docker image
- Isolated environment with its own filesystem, networking, and process space
- Lightweight and ephemeral
- Can be started, stopped, moved, and deleted

### 3. **Dockerfile**
- Text file with instructions to build a Docker image
- Contains commands like FROM, RUN, COPY, CMD, etc.

### 4. **Docker Registry**
- Storage and distribution system for Docker images
- Docker Hub is the default public registry
- Can host private registries

### 5. **Docker Volume**
- Persistent data storage mechanism
- Data persists even after container deletion
- Can be shared between containers

### 6. **Docker Network**
- Enables communication between containers
- Provides isolation and security
- Multiple network drivers (bridge, host, overlay, etc.)

---

## Docker Architecture

Docker uses a **client-server architecture**:

- **Docker Client**: CLI tool that sends commands to Docker daemon
- **Docker Daemon** (dockerd): Manages Docker objects (images, containers, networks, volumes)
- **Docker Registry**: Stores Docker images (Docker Hub, private registries)

### Workflow:
1. Developer writes a Dockerfile
2. Docker builds an image from the Dockerfile
3. Image is pushed to a registry (optional)
4. Docker pulls the image and runs it as a container

---

## Docker Commands Reference

### Image Commands

#### List all local images
```bash
docker images
```
**Explanation**: Displays all Docker images stored locally with details like repository name, tag, image ID, creation date, and size.

**Additional flags**:
```bash
docker images -a        # Show all images (including intermediate)
docker images -q        # Show only image IDs
docker images --filter "dangling=true"  # Show untagged images
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"  # Custom format
```

---

#### Pull an image from Docker Hub
```bash
docker pull <image_name>
docker pull <image_name>:<tag>
```
**Explanation**: Downloads an image from Docker Hub (or specified registry) to your local machine. If no tag is specified, it pulls the `latest` tag.

**Example**:
```bash
docker pull nginx
docker pull nginx:1.25
docker pull ubuntu:22.04
```

---

#### Build an image from a Dockerfile
```bash
docker build -t <image_name>:<version> .
docker build -t <image_name>:<version> . --no-cache
```
**Explanation**: 
- `-t`: Tags the image with a name and optional version
- `.`: Context (current directory) where Dockerfile is located
- `--no-cache`: Builds without using cached layers (clean build)

**Example**:
```bash
docker build -t myapp:1.0 .
docker build -t myapp:latest . --no-cache
docker build -f Dockerfile.prod -t myapp:prod .  # Use specific Dockerfile
```

---

#### Delete an image
```bash
docker rmi <image_name>
docker rmi <image_id>
```
**Explanation**: Removes one or more images from local storage. Image must not be used by any containers.

**Example**:
```bash
docker rmi nginx
docker rmi -f myapp:1.0     # Force removal
docker rmi $(docker images -q)  # Remove all images
```

---

#### Remove unused images
```bash
docker image prune
```
**Explanation**: Removes dangling images (untagged images that aren't used by any container).

**Additional options**:
```bash
docker image prune -a       # Remove all unused images (not just dangling)
docker image prune -a --filter "until=24h"  # Remove images older than 24 hours
```

---

#### Tag an image
```bash
docker tag <source_image>:<tag> <target_image>:<tag>
```
**Explanation**: Creates a new tag for an existing image, useful before pushing to registries.

**Example**:
```bash
docker tag myapp:latest username/myapp:1.0
docker tag myapp:latest myregistry.com/myapp:prod
```

---

#### Save and load images
```bash
docker save -o <filename>.tar <image_name>
docker load -i <filename>.tar
```
**Explanation**: 
- `save`: Exports image to a tar archive
- `load`: Imports image from a tar archive

**Example**:
```bash
docker save -o myapp.tar myapp:latest
docker load -i myapp.tar
```

---

#### Inspect an image
```bash
docker image inspect <image_name>
```
**Explanation**: Shows detailed information about an image in JSON format (layers, configuration, environment variables, etc.).

---

#### Show image history
```bash
docker history <image_name>
```
**Explanation**: Displays the layers and commands used to build an image, useful for debugging and optimization.

---

### Container Commands

#### List containers
```bash
docker ps              # Running containers only
docker ps -a           # All containers (running + stopped)
docker ps -q           # Only container IDs
docker ps -l           # Latest created container
docker ps --filter "status=exited"  # Containers with specific status
```
**Explanation**: Shows container information including ID, image, command, creation time, status, ports, and names.

---

#### Create and run a new container
```bash
docker run <image_name>
```
**Explanation**: Creates and starts a new container from an image. If the image doesn't exist locally, Docker pulls it from Docker Hub.

**Example**:
```bash
docker run nginx
docker run -d nginx                    # Detached mode (background)
docker run -it ubuntu bash             # Interactive mode with terminal
docker run --name mycontainer nginx    # Custom container name
docker run -p 8080:80 nginx            # Port mapping
docker run -e ENV_VAR=value nginx      # Environment variables
docker run --rm nginx                  # Auto-remove after exit
```

---

#### Run container in background (detached mode)
```bash
docker run -d <image_name>
```
**Explanation**: `-d` flag runs the container in the background and prints the container ID.

---

#### Run container with custom name
```bash
docker run --name <container_name> <image_name>
```
**Explanation**: Assigns a custom name to the container instead of a random generated name.

**Example**:
```bash
docker run --name my-nginx nginx
```

---

#### Port binding in container
```bash
docker run -p <host_port>:<container_port> <image_name>
```
**Explanation**: Maps a port on the host machine to a port inside the container, enabling external access.

**Example**:
```bash
docker run -p 8080:80 nginx       # Host port 8080 → Container port 80
docker run -p 3000:3000 myapp     # Same ports
docker run -P nginx               # Map all exposed ports to random host ports
```

---

#### Set environment variables in a container
```bash
docker run -e <var_name>=<var_value> <image_name>
docker run --env-file .env <image_name>
```
**Explanation**: Passes environment variables to the container. Useful for configuration without rebuilding images.

**Example**:
```bash
docker run -e NODE_ENV=production -e PORT=3000 myapp
docker run --env-file ./config.env myapp
```

---

#### Start or stop an existing container
```bash
docker start <container_name>
docker stop <container_name>
docker restart <container_name>
```
**Explanation**: 
- `start`: Starts a stopped container
- `stop`: Gracefully stops a running container (sends SIGTERM)
- `restart`: Stops and starts a container

**Example**:
```bash
docker stop my-nginx
docker start my-nginx
docker restart my-nginx
docker stop $(docker ps -q)  # Stop all running containers
```

---

#### Pause and unpause containers
```bash
docker pause <container_name>
docker unpause <container_name>
```
**Explanation**: Suspends all processes in a container without stopping it, useful for temporarily freeing resources.

---

#### Inspect a running container
```bash
docker inspect <container_name>
```
**Explanation**: Returns detailed JSON information about the container (network settings, mounts, configuration, etc.).

**Example**:
```bash
docker inspect my-nginx
docker inspect --format='{{.NetworkSettings.IPAddress}}' my-nginx  # Get IP address
```

---

#### Delete a container
```bash
docker rm <container_name>
docker rm -f <container_name>    # Force remove (even if running)
docker rm $(docker ps -aq)       # Remove all containers
```
**Explanation**: Removes stopped containers. Use `-f` to force remove running containers.

---

#### Remove all stopped containers
```bash
docker container prune
docker container prune --filter "until=24h"  # Remove containers stopped for 24+ hours
```
**Explanation**: Cleans up stopped containers to free disk space.

---

#### Rename a container
```bash
docker rename <old_name> <new_name>
```
**Explanation**: Changes the name of an existing container.

---

#### Copy files between host and container
```bash
docker cp <container_name>:<container_path> <host_path>
docker cp <host_path> <container_name>:<container_path>
```
**Explanation**: Copies files/directories between the host and container.

**Example**:
```bash
docker cp my-nginx:/etc/nginx/nginx.conf ./nginx.conf
docker cp ./app.js my-container:/app/app.js
```

---

#### View resource usage statistics
```bash
docker stats
docker stats <container_name>
```
**Explanation**: Displays real-time resource usage (CPU, memory, network, disk I/O) for running containers.

---

#### View running processes in a container
```bash
docker top <container_name>
```
**Explanation**: Shows running processes inside a container, similar to the `top` command in Linux.

---

#### Wait for a container to stop
```bash
docker wait <container_name>
```
**Explanation**: Blocks until a container stops, then returns its exit code.

---

#### Attach to a running container
```bash
docker attach <container_name>
```
**Explanation**: Attaches your terminal to a running container's main process. Use `Ctrl+P` then `Ctrl+Q` to detach without stopping.

---

#### Create a new image from a container
```bash
docker commit <container_name> <new_image_name>:<tag>
```
**Explanation**: Creates a new image from a container's current state, capturing all changes made.

**Example**:
```bash
docker commit my-container myapp:v2
```

---

### Docker Hub Commands

#### Login to Docker Hub
```bash
docker login
docker login -u <username>
docker logout
```
**Explanation**: Authenticates with Docker Hub to push/pull private images.

---

#### Push an image to Docker Hub
```bash
docker push <username>/<image_name>:<tag>
```
**Explanation**: Uploads a local image to Docker Hub. Image must be tagged with your username.

**Example**:
```bash
docker tag myapp:latest username/myapp:latest
docker push username/myapp:latest
```

---

#### Search for an image on Docker Hub
```bash
docker search <image_name>
docker search --filter stars=100 nginx  # Filter by stars
```
**Explanation**: Searches Docker Hub for images matching the query.

---

### Volume Commands

#### List all volumes
```bash
docker volume ls
docker volume ls -q  # Only volume names
```
**Explanation**: Displays all Docker volumes on your system.

---

#### Create a new named volume
```bash
docker volume create <volume_name>
```
**Explanation**: Creates a named volume for persistent data storage.

**Example**:
```bash
docker volume create mydata
```

---

#### Delete a named volume
```bash
docker volume rm <volume_name>
docker volume rm $(docker volume ls -q)  # Remove all volumes
```
**Explanation**: Removes a volume. Volume must not be in use by any container.

---

#### Mount named volume with running container
```bash
docker run --volume <volume_name>:<mount_path> <image_name>
docker run --mount type=volume,src=<volume_name>,dest=<mount_path> <image_name>
```
**Explanation**: Attaches a named volume to a container at the specified path. Data persists even after container deletion.

**Example**:
```bash
docker run -v mydata:/app/data nginx
docker run --mount type=volume,src=mydata,dst=/app/data nginx
```

---

#### Mount anonymous volume with running container
```bash
docker run --volume <mount_path> <image_name>
```
**Explanation**: Creates an unnamed volume automatically. Useful for temporary data that should persist during container restarts.

**Example**:
```bash
docker run -v /app/data nginx
```

---

#### Create a bind mount
```bash
docker run --volume <host_path>:<container_path> <image_name>
docker run --mount type=bind,src=<host_path>,dest=<container_path> <image_name>
```
**Explanation**: Maps a directory from the host to the container. Changes are reflected in real-time (useful for development).

**Example**:
```bash
docker run -v $(pwd):/app nginx
docker run -v /home/user/myapp:/app:ro nginx  # Read-only mount
docker run --mount type=bind,src=$(pwd),dst=/app nginx
```

---

#### Inspect a volume
```bash
docker volume inspect <volume_name>
```
**Explanation**: Shows detailed information about a volume (mount point, driver, labels, etc.).

---

#### Remove unused local volumes
```bash
docker volume prune
```
**Explanation**: Removes all anonymous volumes not used by any container.

---

### Network Commands

#### List all networks
```bash
docker network ls
```
**Explanation**: Shows all Docker networks. Default networks include bridge, host, and none.

---

#### Create a network
```bash
docker network create <network_name>
docker network create --driver bridge <network_name>
```
**Explanation**: Creates a custom network for container communication.

**Example**:
```bash
docker network create mynetwork
docker network create --subnet=172.18.0.0/16 mynetwork  # Custom subnet
```

---

#### Connect/Disconnect container to network
```bash
docker network connect <network_name> <container_name>
docker network disconnect <network_name> <container_name>
```
**Explanation**: Adds or removes a container from a network, enabling/disabling communication with other containers.

---

#### Inspect a network
```bash
docker network inspect <network_name>
```
**Explanation**: Shows detailed information about a network (connected containers, subnet, gateway, etc.).

---

#### Remove a network
```bash
docker network rm <network_name>
```
**Explanation**: Deletes a network. No containers should be connected to it.

---

#### Remove all unused networks
```bash
docker network prune
```
**Explanation**: Removes all networks not used by any container.

---

#### Run container with specific network
```bash
docker run --network <network_name> <image_name>
```
**Explanation**: Starts a container and connects it to a specified network.

**Example**:
```bash
docker run --network mynetwork --name web nginx
docker run --network mynetwork --name api node
```

---

### Docker Compose Commands

#### Start services defined in docker-compose.yml
```bash
docker-compose up
docker-compose up -d              # Detached mode
docker-compose up --build         # Rebuild images before starting
docker-compose up --scale web=3   # Scale service to 3 instances
```
**Explanation**: Creates and starts containers defined in the compose file.

---

#### Stop services
```bash
docker-compose down
docker-compose down -v  # Also remove volumes
docker-compose down --rmi all  # Also remove images
```
**Explanation**: Stops and removes containers, networks, and optionally volumes/images.

---

#### View running services
```bash
docker-compose ps
```
**Explanation**: Lists containers for services defined in docker-compose.yml.

---

#### View logs
```bash
docker-compose logs
docker-compose logs -f <service_name>  # Follow logs for specific service
```
**Explanation**: Shows logs from all or specific services.

---

#### Execute command in running service
```bash
docker-compose exec <service_name> <command>
```
**Example**:
```bash
docker-compose exec web bash
docker-compose exec db psql -U postgres
```

---

#### Build or rebuild services
```bash
docker-compose build
docker-compose build --no-cache <service_name>
```
**Explanation**: Builds images for services defined in compose file.

---

#### Start/Stop/Restart services
```bash
docker-compose start
docker-compose stop
docker-compose restart
docker-compose pause
docker-compose unpause
```

---

#### Validate compose file
```bash
docker-compose config
```
**Explanation**: Validates and displays the compose file configuration.

---

### System Commands

#### Display system-wide information
```bash
docker info
```
**Explanation**: Shows system information (containers, images, storage driver, plugins, etc.).

---

#### Show Docker version
```bash
docker version
docker --version
```
**Explanation**: Displays Docker client and server version information.

---

#### Clean up everything (nuclear option)
```bash
docker system prune
docker system prune -a              # Remove all unused images, not just dangling
docker system prune -a --volumes    # Also remove volumes
```
**Explanation**: Removes stopped containers, unused networks, dangling images, and optionally all unused images and volumes.

---

#### Show disk usage
```bash
docker system df
docker system df -v  # Verbose output
```
**Explanation**: Displays how much disk space is used by images, containers, and volumes.

---

#### Monitor real-time events
```bash
docker events
docker events --filter 'type=container'
```
**Explanation**: Shows real-time events from the Docker daemon (container starts/stops, image pulls, etc.).

---

### Troubleshooting Commands

#### Fetch logs of a container
```bash
docker logs <container_name>
docker logs -f <container_name>          # Follow logs (like tail -f)
docker logs --tail 100 <container_name>  # Last 100 lines
docker logs --since 30m <container_name> # Last 30 minutes
docker logs -t <container_name>          # With timestamps
```
**Explanation**: Views container logs, essential for debugging application issues.

---

#### Open shell inside running container
```bash
docker exec -it <container_name> /bin/bash
docker exec -it <container_name> sh
docker exec -it <container_name> /bin/sh
```
**Explanation**: 
- `-i`: Interactive mode
- `-t`: Allocate a pseudo-TTY (terminal)
- Opens a shell session inside the running container for debugging

**Example**:
```bash
docker exec -it my-nginx bash
docker exec -it alpine-container sh  # For Alpine Linux
docker exec my-nginx ls -la /app     # Run single command without shell
```

---

#### View container port mappings
```bash
docker port <container_name>
```
**Explanation**: Shows port mappings for a container.

---

#### Export container filesystem
```bash
docker export <container_name> > container.tar
```
**Explanation**: Exports container's filesystem as a tar archive.

---

#### Import container filesystem
```bash
docker import <tarball> <image_name>:<tag>
```
**Explanation**: Creates an image from a tarball.

---

#### View changes to container filesystem
```bash
docker diff <container_name>
```
**Explanation**: Shows files that have been added (A), deleted (D), or changed (C) in the container.

---

## Dockerfile

A **Dockerfile** is a script containing instructions to build a Docker image.

### Basic Dockerfile Structure

```dockerfile
# Base image
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy dependency files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy application code
COPY . .

# Expose port
EXPOSE 3000

# Define environment variable
ENV NODE_ENV=production

# Run application
CMD ["node", "app.js"]
```

### Common Dockerfile Instructions

| Instruction | Purpose | Example |
|-------------|---------|---------|
| `FROM` | Base image | `FROM ubuntu:22.04` |
| `WORKDIR` | Set working directory | `WORKDIR /app` |
| `COPY` | Copy files from host to image | `COPY . /app` |
| `ADD` | Like COPY but can extract archives | `ADD app.tar.gz /app` |
| `RUN` | Execute command during build | `RUN npm install` |
| `CMD` | Default command to run container | `CMD ["python", "app.py"]` |
| `ENTRYPOINT` | Configure container as executable | `ENTRYPOINT ["nginx"]` |
| `EXPOSE` | Document which ports to expose | `EXPOSE 80` |
| `ENV` | Set environment variables | `ENV PORT=3000` |
| `ARG` | Build-time variables | `ARG VERSION=1.0` |
| `VOLUME` | Create mount point | `VOLUME /data` |
| `USER` | Set user for RUN, CMD, ENTRYPOINT | `USER node` |
| `LABEL` | Add metadata | `LABEL version="1.0"` |
| `HEALTHCHECK` | Check container health | `HEALTHCHECK CMD curl -f http://localhost/` |

### Multi-Stage Build Example

```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["node", "dist/app.js"]
```

**Benefits**: Smaller final image, build dependencies not included in production image.

---

## Docker Compose

**Docker Compose** is a tool for defining and running multi-container applications using a YAML configuration file.

### Basic docker-compose.yml Structure

```yaml
version: '3.8'

services:
  web:
    build: ./web
    ports:
      - "8080:80"
    environment:
      - NODE_ENV=production
    volumes:
      - ./app:/usr/share/nginx/html
    depends_on:
      - api
    networks:
      - frontend
      - backend

  api:
    image: node:18
    working_dir: /app
    volumes:
      - ./api:/app
    command: npm start
    environment:
      - DATABASE_URL=postgres://db:5432
    depends_on:
      - db
    networks:
      - backend

  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: myapp
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend

volumes:
  db-data:

networks:
  frontend:
  backend:
```

### Key Compose File Sections

- **services**: Define containers
- **volumes**: Named volumes for data persistence
- **networks**: Custom networks for service communication
- **build**: Build configuration for custom images
- **depends_on**: Define service startup order
- **environment**: Environment variables
- **ports**: Port mappings

### Advanced Compose Features

```yaml
services:
  app:
    image: myapp:latest
    deploy:
      replicas: 3              # Scale to 3 instances
      restart_policy:
        condition: on-failure
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
```

---

## Best Practices

### Dockerfile Best Practices

1. **Use specific base image tags**
   ```dockerfile
   # Good
   FROM node:18.17-alpine
   
   # Avoid
   FROM node:latest
   ```

2. **Minimize layers** - Combine RUN commands
   ```dockerfile
   RUN apt-get update && \
       apt-get install -y curl && \
       apt-get clean && \
       rm -rf /var/lib/apt/lists/*
   ```

3. **Use .dockerignore** to exclude unnecessary files
   ```
   node_modules
   .git
   .env
   *.md
   ```

4. **Order instructions by change frequency** (most stable first)
   ```dockerfile
   FROM node:18
   WORKDIR /app
   COPY package*.json ./    # Changes rarely
   RUN npm install
   COPY . .                 # Changes often
   ```

5. **Don't run containers as root**
   ```dockerfile
   RUN addgroup -S appgroup && adduser -S appuser -G appgroup
   USER appuser
   ```

6. **Use multi-stage builds** to reduce image size

7. **Scan images for vulnerabilities**
   ```bash
   docker scan myapp:latest
   ```

### Container Best Practices

1. **One process per container** (microservices principle)
2. **Use environment variables** for configuration
3. **Health checks** to ensure container is working
4. **Resource limits** to prevent resource exhaustion
5. **Always use volumes** for persistent data
6. **Use named volumes over bind mounts** in production
7. **Don't store secrets in images** - use Docker secrets or environment variables

### Security Best Practices

1. **Use official base images** from trusted sources
2. **Regularly update base images** to get security patches
3. **Scan images for vulnerabilities** regularly
4. **Don't store sensitive data in images**
5. **Use read-only filesystems** when possible
6. **Limit container capabilities**
7. **Use user namespaces** to map container root to non-root host user

### Performance Best Practices

1. **Optimize layer caching** by ordering Dockerfile instructions properly
2. **Use smaller base images** (Alpine Linux variants)
3. **Remove unnecessary files** in the same RUN command
4. **Use multi-stage builds** to exclude build dependencies
5. **Limit container resources** (CPU, memory) appropriately

---

## Quick Reference

### Container Lifecycle
```bash
create → start → running → pause → unpause → stop → kill → remove
```

### Common Workflow
```bash
# 1. Write Dockerfile
# 2. Build image
docker build -t myapp:1.0 .

# 3. Run container
docker run -d -p 8080:80 --name myapp-container myapp:1.0

# 4. Check logs
docker logs myapp-container

# 5. Stop container
docker stop myapp-container

# 6. Remove container
docker rm myapp-container
```

### Useful One-Liners
```bash
# Stop all running containers
docker stop $(docker ps -q)

# Remove all containers
docker rm $(docker ps -aq)

# Remove all images
docker rmi $(docker images -q)

# Remove all volumes
docker volume rm $(docker volume ls -q)

# Complete cleanup
docker system prune -a --volumes

# Get container IP address
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name

# Execute command in all running containers
docker ps -q | xargs -I {} docker exec {} command
```

---

## Additional Resources

- **Official Documentation**: https://docs.docker.com
- **Docker Hub**: https://hub.docker.com
- **Docker Compose Docs**: https://docs.docker.com/compose
- **Best Practices**: https://docs.docker.com/develop/dev-best-practices
- **Security**: https://docs.docker.com/engine/security

---

## Conclusion

Docker revolutionizes application deployment by providing:
- **Consistency**: Applications run the same everywhere
- **Efficiency**: Lightweight and fast compared to VMs
- **Isolation**: Each application runs independently
- **Portability**: Build once, run anywhere
- **Scalability**: Easy horizontal scaling

Whether you're developing locally, testing in CI/CD pipelines, or deploying to production, Docker provides the tools and consistency needed for modern application development.

---

## Common Use Cases

### 1. Local Development Environment
```bash
# Run a local database for development
docker run -d \
  --name dev-postgres \
  -e POSTGRES_PASSWORD=devpass \
  -e POSTGRES_DB=myapp_dev \
  -p 5432:5432 \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15
```

### 2. Microservices Application
```yaml
# docker-compose.yml for microservices
version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - api-gateway

  api-gateway:
    build: ./api-gateway
    ports:
      - "8080:8080"
    depends_on:
      - auth-service
      - user-service

  auth-service:
    build: ./auth-service
    environment:
      - JWT_SECRET=${JWT_SECRET}
      - DB_HOST=postgres

  user-service:
    build: ./user-service
    environment:
      - DB_HOST=postgres

  postgres:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - db-data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine

volumes:
  db-data:
```

### 3. CI/CD Pipeline
```bash
# Build and test in CI
docker build -t myapp:${CI_COMMIT_SHA} .
docker run myapp:${CI_COMMIT_SHA} npm test

# Push to registry
docker tag myapp:${CI_COMMIT_SHA} registry.example.com/myapp:latest
docker push registry.example.com/myapp:latest
```

### 4. Running Legacy Applications
```dockerfile
# Containerize an old PHP application
FROM php:5.6-apache
COPY . /var/www/html/
RUN docker-php-ext-install mysql
EXPOSE 80
```

---

## Troubleshooting Common Issues

### Issue 1: Container Exits Immediately
**Problem**: Container starts but exits right away

**Solution**:
```bash
# Check exit code and logs
docker ps -a
docker logs <container_name>

# Common causes:
# - Application crashes on startup
# - No foreground process running
# - Configuration errors
```

### Issue 2: Port Already in Use
**Problem**: `bind: address already in use`

**Solution**:
```bash
# Find what's using the port
sudo lsof -i :8080
# or
sudo netstat -tulpn | grep 8080

# Use a different host port
docker run -p 8081:80 nginx
```

### Issue 3: Permission Denied
**Problem**: Permission errors when accessing files

**Solution**:
```bash
# Run as specific user
docker run --user $(id -u):$(id -g) myapp

# Or in Dockerfile
RUN chown -R appuser:appuser /app
USER appuser
```

### Issue 4: Out of Disk Space
**Problem**: No space left on device

**Solution**:
```bash
# Clean up everything
docker system prune -a --volumes

# Check disk usage
docker system df -v

# Remove specific items
docker volume prune
docker image prune -a
```

### Issue 5: Container Can't Connect to Another Container
**Problem**: Containers can't communicate

**Solution**:
```bash
# Ensure containers are on same network
docker network create mynetwork
docker run --network mynetwork --name web nginx
docker run --network mynetwork --name api node

# Use container names as hostnames
# Inside 'api' container: curl http://web
```

### Issue 6: Slow Build Times
**Problem**: Docker build takes too long

**Solution**:
```bash
# Use .dockerignore to exclude files
# Order Dockerfile instructions properly
# Use multi-stage builds
# Enable BuildKit
export DOCKER_BUILDKIT=1
docker build -t myapp .
```

### Issue 7: Changes Not Reflected in Container
**Problem**: Code changes not showing up

**Solution**:
```bash
# For bind mounts, ensure correct path
docker run -v $(pwd):/app myapp

# For images, rebuild
docker build -t myapp .
docker run myapp

# Or use volumes with hot reload (development)
docker run -v $(pwd):/app myapp npm run dev
```

---

## Docker Networking Deep Dive

### Network Types

1. **Bridge (default)**: Isolated network for containers on same host
2. **Host**: Container uses host's network directly (no isolation)
3. **None**: No networking
4. **Overlay**: Multi-host networking (Docker Swarm)
5. **Macvlan**: Assign MAC address to containers

### Network Commands Explained

```bash
# Create custom bridge network
docker network create --driver bridge my-bridge-net

# Create network with custom subnet
docker network create --subnet=172.20.0.0/16 --gateway=172.20.0.1 my-custom-net

# Run container on specific network with static IP
docker run --network my-custom-net --ip 172.20.0.10 nginx

# Connect running container to additional network
docker network connect my-bridge-net existing-container

# Inspect network to see connected containers
docker network inspect my-bridge-net

# Remove network
docker network disconnect my-bridge-net existing-container
docker network rm my-bridge-net
```

### Container Communication Example

```bash
# Create network
docker network create app-network

# Run database
docker run -d \
  --name mydb \
  --network app-network \
  -e POSTGRES_PASSWORD=secret \
  postgres:15

# Run application (can connect to database via hostname 'mydb')
docker run -d \
  --name myapp \
  --network app-network \
  -e DB_HOST=mydb \
  -e DB_PASSWORD=secret \
  myapp:latest

# Inside myapp container, you can now:
# psql -h mydb -U postgres
```

---

## Docker Volumes Deep Dive

### Volume Types Comparison

| Type | Use Case | Persistence | Performance | Example |
|------|----------|-------------|-------------|---------|
| **Named Volume** | Production data | High | Best | `docker run -v mydata:/data` |
| **Anonymous Volume** | Temporary data | Medium | Best | `docker run -v /data` |
| **Bind Mount** | Development | High | Good | `docker run -v $(pwd):/app` |
| **tmpfs** | Sensitive data | None | Excellent | `docker run --tmpfs /tmp` |

### Volume Examples

```bash
# Named volume (recommended for production)
docker volume create app-data
docker run -v app-data:/var/lib/app myapp

# Bind mount (development with hot reload)
docker run -v $(pwd)/src:/app/src myapp

# Read-only bind mount
docker run -v $(pwd)/config:/app/config:ro myapp

# tmpfs mount (in-memory, for sensitive data)
docker run --tmpfs /tmp:rw,size=100m myapp

# Volume with specific driver
docker volume create --driver local \
  --opt type=nfs \
  --opt o=addr=192.168.1.1,rw \
  --opt device=:/path/to/dir \
  nfs-volume

# Backup volume data
docker run --rm \
  -v app-data:/data \
  -v $(pwd):/backup \
  alpine tar czf /backup/backup.tar.gz /data

# Restore volume data
docker run --rm \
  -v app-data:/data \
  -v $(pwd):/backup \
  alpine tar xzf /backup/backup.tar.gz -C /
```

---

## Advanced Docker Features

### 1. Health Checks

```dockerfile
# In Dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost/ || exit 1
```

```bash
# Check health status
docker ps
docker inspect --format='{{.State.Health.Status}}' container_name
```

### 2. Resource Constraints

```bash
# Limit memory
docker run -m 512m myapp

# Limit CPU
docker run --cpus="1.5" myapp

# Limit CPU shares (relative weight)
docker run --cpu-shares=512 myapp

# Limit block IO
docker run --device-read-bps /dev/sda:1mb myapp

# View resource usage
docker stats myapp
```

### 3. Docker Secrets (Swarm mode)

```bash
# Create secret
echo "my_secret_password" | docker secret create db_password -

# Use in service
docker service create \
  --name myapp \
  --secret db_password \
  myapp:latest

# Access in container at /run/secrets/db_password
```

### 4. Docker Contexts

```bash
# List contexts
docker context ls

# Create new context (remote Docker host)
docker context create remote-server \
  --docker "host=ssh://user@remote-server"

# Switch context
docker context use remote-server

# Now all commands run on remote server
docker ps
```

### 5. BuildKit Features

```bash
# Enable BuildKit
export DOCKER_BUILDKIT=1

# Cache mount (speed up builds)
RUN --mount=type=cache,target=/root/.npm npm install

# Secret mount (use secrets during build)
RUN --mount=type=secret,id=npm_token \
    npm config set //registry.npmjs.org/:_authToken=$(cat /run/secrets/npm_token)

# SSH mount (clone private repos)
RUN --mount=type=ssh git clone git@github.com:user/private-repo.git
```

### 6. Multi-Platform Builds

```bash
# Build for multiple architectures
docker buildx create --name multiplatform --use
docker buildx build --platform linux/amd64,linux/arm64,linux/arm/v7 \
  -t myapp:latest --push .
```

---

## Docker Compose Advanced Features

### Environment Variables

```yaml
# docker-compose.yml
services:
  web:
    image: myapp:latest
    environment:
      - NODE_ENV=${NODE_ENV:-production}  # Default value
      - API_KEY=${API_KEY}
    env_file:
      - .env
      - .env.production
```

```bash
# .env file
NODE_ENV=development
API_KEY=secret123
DATABASE_URL=postgres://localhost/mydb
```

### Extends and Override

```yaml
# docker-compose.yml (base)
services:
  web:
    image: myapp:latest
    ports:
      - "8080:80"

# docker-compose.override.yml (auto-loaded in development)
services:
  web:
    volumes:
      - ./src:/app/src
    environment:
      - DEBUG=true

# docker-compose.prod.yml (use with -f flag)
services:
  web:
    deploy:
      replicas: 3
```

```bash
# Use specific compose file
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up
```

### Profiles

```yaml
services:
  web:
    image: nginx
    profiles: ["frontend"]
  
  api:
    image: node:18
    profiles: ["backend"]
  
  db:
    image: postgres:15
    # No profile - always started
```

```bash
# Start specific profiles
docker-compose --profile frontend up
docker-compose --profile backend --profile frontend up
```

---

## Docker Registry (Private)

### Setup Local Registry

```bash
# Run registry container
docker run -d -p 5000:5000 --name registry registry:2

# Tag image for local registry
docker tag myapp:latest localhost:5000/myapp:latest

# Push to local registry
docker push localhost:5000/myapp:latest

# Pull from local registry
docker pull localhost:5000/myapp:latest
```

### Secure Registry with TLS

```bash
# Generate certificates
openssl req -newkey rsa:4096 -nodes -sha256 \
  -keyout domain.key -x509 -days 365 -out domain.crt

# Run registry with TLS
docker run -d -p 5000:5000 \
  --name secure-registry \
  -v $(pwd)/certs:/certs \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2
```

---

## Docker Logging

### Logging Drivers

```bash
# JSON file (default)
docker run --log-driver json-file --log-opt max-size=10m --log-opt max-file=3 myapp

# Syslog
docker run --log-driver syslog --log-opt syslog-address=tcp://192.168.1.10:514 myapp

# Journald
docker run --log-driver journald myapp

# Disable logging
docker run --log-driver none myapp
```

### Configure in daemon.json

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3",
    "labels": "production"
  }
}
```

### View logs with filters

```bash
# Last 100 lines
docker logs --tail 100 myapp

# Last 30 minutes
docker logs --since 30m myapp

# Between timestamps
docker logs --since 2025-01-01T00:00:00 --until 2025-01-02T00:00:00 myapp

# With timestamps
docker logs -t myapp

# Follow logs
docker logs -f myapp
```

---

## Performance Optimization Tips

### 1. Reduce Image Size

```dockerfile
# Use Alpine base images
FROM node:18-alpine

# Multi-stage builds
FROM node:18 AS builder
WORKDIR /app
COPY . .
RUN npm run build

FROM node:18-alpine
COPY --from=builder /app/dist ./dist
CMD ["node", "dist/app.js"]

# Remove unnecessary files
RUN npm install --production && \
    npm cache clean --force && \
    rm -rf /tmp/*
```

### 2. Optimize Layer Caching

```dockerfile
# Copy dependency files first (changes less frequently)
COPY package*.json ./
RUN npm install

# Copy source code last (changes frequently)
COPY . .
```

### 3. Use .dockerignore

```
node_modules
.git
.env
.DS_Store
*.md
tests/
.vscode/
```

### 4. Minimize Running Processes

```bash
# Use exec form for CMD/ENTRYPOINT (avoids shell process)
CMD ["node", "app.js"]  # Good
CMD node app.js          # Bad (creates shell process)
```

### 5. Use BuildKit Cache Mounts

```dockerfile
# Speeds up npm install
RUN --mount=type=cache,target=/root/.npm npm install
```

---

## Monitoring and Debugging

### Container Inspection

```bash
# Full container details
docker inspect myapp | jq .

# Specific information
docker inspect --format='{{.NetworkSettings.IPAddress}}' myapp
docker inspect --format='{{.State.Status}}' myapp
docker inspect --format='{{json .Config.Env}}' myapp | jq .
```

### Real-time Monitoring

```bash
# Resource usage
docker stats

# Process list
docker top myapp

# Events
docker events --filter container=myapp

# System info
docker info
```

### Debugging Running Container

```bash
# Execute shell
docker exec -it myapp /bin/bash

# Run specific command
docker exec myapp ps aux
docker exec myapp cat /etc/os-release

# Copy files for inspection
docker cp myapp:/var/log/app.log ./app.log

# Check filesystem changes
docker diff myapp
```

---

## Migration and Backup

### Export/Import

```bash
# Export container as tar
docker export myapp > myapp.tar

# Import tar as image
cat myapp.tar | docker import - myapp:backup
```

### Save/Load Images

```bash
# Save image to tar
docker save -o myapp.tar myapp:latest

# Load image from tar
docker load -i myapp.tar

# Save multiple images
docker save -o images.tar myapp:latest postgres:15 nginx:alpine
```

### Volume Backup/Restore

```bash
# Backup volume
docker run --rm \
  -v mydata:/data \
  -v $(pwd):/backup \
  alpine tar czf /backup/mydata-backup.tar.gz -C /data .

# Restore volume
docker volume create mydata-restored
docker run --rm \
  -v mydata-restored:/data \
  -v $(pwd):/backup \
  alpine tar xzf /backup/mydata-backup.tar.gz -C /data
```

---

## Summary of Key Concepts

1. **Images**: Read-only templates for containers
2. **Containers**: Running instances of images
3. **Volumes**: Persistent storage mechanism
4. **Networks**: Enable container communication
5. **Dockerfile**: Instructions to build images
6. **Docker Compose**: Multi-container orchestration
7. **Registry**: Storage for Docker images

### Command Hierarchy

```
docker
├── image (images, build, pull, push, tag)
├── container (run, start, stop, rm, exec, logs)
├── volume (create, ls, rm, prune)
├── network (create, ls, rm, connect, disconnect)
├── system (info, df, prune, events)
└── compose (up, down, ps, logs, exec)
```

---

## Additional Resources

- **Official Documentation**: https://docs.docker.com
- **Docker Hub**: https://hub.docker.com
- **Docker Compose Docs**: https://docs.docker.com/compose
- **Best Practices**: https://docs.docker.com/develop/dev-best-practices
- **Security**: https://docs.docker.com/engine/security
- **Dockerfile Reference**: https://docs.docker.com/engine/reference/builder
- **Docker CLI Reference**: https://docs.docker.com/engine/reference/commandline/cli

---

**Happy Dockerizing! 🐋**
