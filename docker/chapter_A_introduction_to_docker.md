# Chapter 0: Introduction to Docker

A comprehensive guide to Docker terminology and concepts, with point-to-point explanations and examples.

---

## 1. What is Docker?

- **Docker** is a platform for developing, shipping, and running applications in **containers**.
- Containers package your app and its dependencies into a single, runnable unit that behaves the same on any machine that has Docker installed.
- **Point:** Think of it as “it works on my machine” → “it works everywhere, the same way.”

**Minimal example — run a one-off command in a container:**

```bash
docker run --rm alpine echo "Hello, Docker"
```

- `alpine` is a tiny Linux image; Docker downloads it (if needed), starts a container, runs `echo`, then removes the container (`--rm`).

---

## 2. Core Terminology

### 2.1 Image

- An **image** is a read-only template used to create containers. It includes the OS, runtime, app code, and dependencies.
- Images are built in **layers**; each instruction in a Dockerfile typically adds a layer.
- Images are identified by **name**, optional **tag** (e.g. `node:18`), and a unique **ID** (digest).

**Examples:**

```bash
# List local images
docker images

# Pull an image (download from a registry)
docker pull node:18-alpine

# Inspect image layers and metadata
docker history node:18-alpine
docker inspect node:18-alpine
```

**Point:** You **build** or **pull** images; you **run** containers from them.

---

### 2.2 Container

- A **container** is a runnable instance of an image. You can have many containers from the same image.
- Containers are isolated (processes, filesystem, network) but can share the host kernel.
- Containers have a **lifecycle**: create → start → (running) → stop → (optional) remove.

**Examples:**

```bash
# Run a new container (creates + starts)
docker run -d --name my-nginx nginx:alpine

# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Stop and remove
docker stop my-nginx
docker rm my-nginx
```

**Point:** Image = blueprint; container = running process(es) created from that blueprint.

---

### 2.3 Dockerfile

- A **Dockerfile** is a text file of instructions used to **build** an image. Each instruction usually creates one **layer** in the image.
- Common instructions: `FROM`, `RUN`, `COPY`, `ADD`, `WORKDIR`, `ENV`, `EXPOSE`, `CMD`, `ENTRYPOINT`.

**Example Dockerfile:**

```dockerfile
# Base image (required first instruction)
FROM node:18-alpine

# Set working directory inside the container
WORKDIR /app

# Copy package files first (better layer caching)
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application code
COPY . .

# Expose port (documentation; does not publish the port)
EXPOSE 3000

# Default command when container runs
CMD ["node", "server.js"]
```

**Point:** Dockerfile defines _how_ to build the image; `docker build` executes it.

---

### 2.4 Dockerfile Instructions (Reference)

| Instruction   | Purpose                                            | Example                                         |
| ------------- | -------------------------------------------------- | ----------------------------------------------- |
| `FROM`        | Base image (required first line)                   | `FROM python:3.11-slim`                         |
| `RUN`         | Run a command during build                         | `RUN apt-get update && apt-get install -y curl` |
| `COPY`        | Copy files from build context into image           | `COPY . /app`                                   |
| `ADD`         | Like COPY, but can fetch URLs and extract archives | `ADD https://example.com/file.tar.gz /tmp/`     |
| `WORKDIR`     | Set current directory for later instructions       | `WORKDIR /app`                                  |
| `ENV`         | Set environment variable                           | `ENV NODE_ENV=production`                       |
| `ARG`         | Build-time variable (not in final image)           | `ARG VERSION=1.0`                               |
| `EXPOSE`      | Document which port the app listens on             | `EXPOSE 8080`                                   |
| `CMD`         | Default command when container runs (overridable)  | `CMD ["npm", "start"]`                          |
| `ENTRYPOINT`  | Main executable (harder to override)               | `ENTRYPOINT ["docker-entrypoint.sh"]`           |
| `USER`        | Run subsequent instructions as this user           | `USER node`                                     |
| `VOLUME`      | Declare a mount point for external storage         | `VOLUME /data`                                  |
| `HEALTHCHECK` | Define how to check if container is healthy        | See Health check section                        |

**Point:** Prefer `COPY` over `ADD` unless you need URL download or archive extraction. Use `RUN` for installing packages; chain with `&&` to avoid extra layers.

---

### 2.5 Build context and .dockerignore

- **Build context** is the set of files you send to the Docker daemon when you run `docker build`. By default it’s the current directory (`.`).
- Only files in the build context can be `COPY`/`ADD`-ed. Large or sensitive files slow down builds and can leak into the image if not excluded.

**Example:**

```bash
# Build using current directory as context; image tagged as myapp:v1
docker build -t myapp:v1 .
```

- **`.dockerignore`** works like `.gitignore`: listed files/directories are excluded from the build context.

**Example `.dockerignore`:**

```
node_modules
.git
.env
*.log
Dockerfile
.dockerignore
```

**Point:** Use `.dockerignore` to keep the context small and avoid copying secrets or unnecessary files.

---

### 2.6 Tag

- A **tag** is a label for an image, usually indicating version or variant (e.g. `node:18`, `myapp:latest`).
- Format: `[registry/][repository/]name[:tag]`. If tag is omitted, Docker uses `latest`.

**Examples:**

```bash
# Tag a local image
docker tag myapp:latest myapp:1.0.0

# Tag for a registry
docker tag myapp:1.0.0 ghcr.io/myorg/myapp:1.0.0

# Build with a tag
docker build -t myapp:dev .
```

**Point:** Always tag meaningfully (e.g. semver or commit SHA); avoid relying on `latest` in production.

---

### 2.7 Registry and Repository

- A **registry** is a storage and distribution service for images (e.g. Docker Hub, GitHub Container Registry, AWS ECR).
- A **repository** is a collection of related images, usually under one name and different tags (e.g. `library/node` has tags `18`, `20`, `alpine`).

**Examples:**

```bash
# Log in to Docker Hub
docker login

# Push to Docker Hub (repository = username/name)
docker push myusername/myapp:1.0.0

# Pull from a specific registry
docker pull ghcr.io/myorg/myapp:latest
```

**Point:** Registry = “where”; repository = “which set of images (name + tags).”

---

### 2.8 Docker Engine (Daemon) and Client

- **Docker Engine** is the background service (daemon) that builds images, runs containers, and manages storage and networks. On Linux it’s often `dockerd`.
- The **Docker client** (`docker`) is the CLI you use; it sends commands to the daemon (locally or remotely via API).

**Point:** When you run `docker run`, the client talks to the daemon; the daemon creates and runs the container.

---

### 2.9 Volume

- A **volume** is Docker-managed storage that persists after containers are removed. Data lives outside the container’s writable layer.
- Use volumes for databases, caches, or any data that must survive container restarts or removal.

**Examples:**

```bash
# Create a named volume
docker volume create mydata

# Run a container that uses it
docker run -d --name db -v mydata:/var/lib/postgresql/data postgres:15

# List volumes
docker volume ls

# Inspect volume (mount point on host)
docker volume inspect mydata
```

**Point:** Named volumes are the standard way to persist data in Docker; paths inside the container are fixed (e.g. `/var/lib/postgresql/data`).

---

### 2.10 Bind mount

- A **bind mount** maps a specific path on the host into the container. Good for development (live code) or injecting config.
- Unlike a volume, the path is controlled by you and can be anywhere on the host.

**Example:**

```bash
# Mount host ./src into container /app/src (e.g. for live reload)
docker run -d -v $(pwd)/src:/app/src myapp:dev

# Read-only bind mount
docker run -v /etc/hosts:/etc/hosts:ro alpine cat /etc/hosts
```

**Point:** Bind mount = “this host folder _is_ this container folder”; volume = “Docker-managed storage attached to this container path.”

---

### 2.11 Network

- Docker provides **networks** so containers can talk to each other by name (DNS) and isolate groups of containers.
- Each network has a driver: `bridge` (default), `host`, `none`, or overlay/plugins.

**Examples:**

```bash
# List networks
docker network ls

# Create a custom bridge network
docker network create mynet

# Run two containers on the same network
docker run -d --name web --network mynet nginx:alpine
docker run -d --name app --network mynet myapp:latest

# From inside `app`, you can reach nginx as: http://web:80
```

**Point:** Put related containers on the same user-defined network so they can use container names as hostnames.

---

### 2.12 Port mapping (publish)

- **Publishing a port** maps a container port to a port on the host so external traffic can reach the app.

**Examples:**

```bash
# Map host 8080 → container 80
docker run -d -p 8080:80 nginx:alpine

# Map host port to same container port (host picks an ephemeral port if 80 is busy)
docker run -d -p 80 nginx:alpine

# Bind to a specific host IP
docker run -d -p 127.0.0.1:8080:80 nginx:alpine
```

**Point:** `-p hostPort:containerPort` makes the container’s port visible on the host; without `-p`, the port is only reachable from other containers on the same network.

---

### 2.13 Environment variables

- You can set **environment variables** for a container at runtime; the app reads them like any OS env vars.

**Examples:**

```bash
# One variable
docker run -e NODE_ENV=production myapp:latest

# Multiple
docker run -e NODE_ENV=production -e PORT=3000 myapp:latest

# From host environment
docker run -e NODE_ENV myapp:latest

# From a file
docker run --env-file .env myapp:latest
```

**Point:** Use `-e` or `--env-file` for config and secrets (prefer secrets management in production over plain env files).

---

### 2.14 CMD vs ENTRYPOINT

- **CMD** is the default command and arguments when the container starts. It’s easily overridden: `docker run myapp npm run test`.
- **ENTRYPOINT** is the fixed executable; arguments you pass to `docker run` are appended to ENTRYPOINT (unless you use `--entrypoint`).

**Example:**

```dockerfile
# CMD: default is "node server.js"; can override to "node test.js"
CMD ["node", "server.js"]

# ENTRYPOINT: container always runs "node"; run args become node's args
ENTRYPOINT ["node"]
CMD ["server.js"]   # default argument if none given
```

```bash
docker run myapp              # runs: node server.js
docker run myapp test.js      # runs: node test.js
```

**Point:** Use ENTRYPOINT for “this container is always this binary”; use CMD for default arguments that users might override.

---

### 2.15 Docker Compose

- **Docker Compose** defines and runs multi-container apps via a YAML file (`docker-compose.yml` or `compose.yaml`). It creates networks, volumes, and containers from one place.

**Example `compose.yaml`:**

```yaml
services:
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - REDIS_URL=redis://redis:6379
    depends_on:
      - redis
    networks:
      - appnet

  redis:
    image: redis:7-alpine
    volumes:
      - redisdata:/data
    networks:
      - appnet

volumes:
  redisdata: {}

networks:
  appnet: {}
```

**Commands:**

```bash
docker compose up -d      # Build (if needed), create network/volumes, start containers
docker compose down       # Stop and remove containers; add -v to remove volumes
docker compose ps
docker compose logs -f web
```

**Point:** Compose is the standard way to run several related services (app + DB + cache) with one file and one CLI.

---

### 2.16 Multi-stage build

- A **multi-stage build** uses multiple `FROM` instructions. Only the final stage ends up in the image; earlier stages are for building (compilers, dev deps) and are discarded.
- Result: smaller, more secure production images.

**Example:**

```dockerfile
# Stage 1: build
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: production
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./
USER node
CMD ["node", "dist/index.js"]
```

**Point:** Build in one stage, copy only artifacts into a minimal final stage; the image won’t contain build tools or source.

---

### 2.17 Health check

- **HEALTHCHECK** tells Docker how to test if the container is still working. Docker can mark the container as unhealthy and orchestrators can act on it.

**Example in Dockerfile:**

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget -q -O- http://localhost:3000/health || exit 1
```

**Example in `compose.yaml`:**

```yaml
services:
  web:
    image: myapp:latest
    healthcheck:
      test: ["CMD", "wget", "-q", "-O-", "http://localhost:3000/health"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 10s
```

**Point:** Use HEALTHCHECK for long-running services so load balancers and orchestrators can stop sending traffic to broken containers.

---

### 2.18 Restart policy

- **Restart policy** controls what happens when the container exits: don’t restart, restart always, restart on failure, or restart unless explicitly stopped.

**Examples:**

```bash
# Restart always (e.g. keep a service up)
docker run -d --restart always nginx:alpine

# Restart only on non-zero exit (up to Docker daemon restart)
docker run -d --restart on-failure redis:alpine

# In Compose
# restart: always | on-failure | unless-stopped
```

**Point:** Use `always` or `unless-stopped` for services that should survive reboots and crashes.

---

### 2.19 docker run vs docker create + start

- **`docker run`** = create + start in one step. Common for one-off or interactive containers.
- **`docker create`** creates a container but does not start it; **`docker start`** starts it (optionally with `-a` to attach).

**Examples:**

```bash
# Create then start (useful for pre-configuring before first start)
docker create --name mybox alpine sleep 3600
docker start mybox

# Equivalent one-liner
docker run -d --name mybox alpine sleep 3600
```

**Point:** Use `create` + `start` when you need to tweak the container before first run; otherwise `run` is enough.

---

### 2.20 Exec vs Run

- **`docker run`** creates and starts a **new** container from an image.
- **`docker exec`** runs a command in an **existing** running container.

**Examples:**

```bash
# New container
docker run --rm alpine echo "hello"

# Command inside a container that’s already running
docker exec my-nginx cat /etc/nginx/nginx.conf
docker exec -it my-nginx sh   # interactive shell
```

**Point:** `run` = new container; `exec` = “run this inside that container.”

---

### 2.21 Layer and image build cache

- Each Dockerfile instruction that changes the filesystem creates a **layer**. Layers are cached; if an instruction and everything before it are unchanged, Docker reuses the cache.
- Order matters: put rarely changed steps (e.g. dependency install) first and frequently changed steps (e.g. `COPY . .`) last.

**Example:**

```dockerfile
# Good: dependency layer is cached when only app code changes
COPY package*.json ./
RUN npm ci
COPY . .

# Bad: any code change invalidates the cache for npm ci
COPY . .
RUN npm ci
```

**Point:** Optimize Dockerfile order and use `.dockerignore` so small code changes don’t invalidate heavy layers.

---

## 3. Quick reference: common commands

| Goal                       | Command                                        |
| -------------------------- | ---------------------------------------------- |
| Build image                | `docker build -t name:tag .`                   |
| Run container (foreground) | `docker run --rm name`                         |
| Run container (detached)   | `docker run -d --name c1 name`                 |
| Publish port               | `docker run -d -p 8080:80 name`                |
| Mount volume               | `docker run -d -v volname:/path name`          |
| Set env                    | `docker run -e KEY=val name`                   |
| List containers            | `docker ps` / `docker ps -a`                   |
| List images                | `docker images`                                |
| Stop/remove                | `docker stop c1` / `docker rm c1`              |
| Run command in container   | `docker exec c1 cmd`                           |
| Shell in container         | `docker exec -it c1 sh`                        |
| View logs                  | `docker logs -f c1`                            |
| Compose up/down            | `docker compose up -d` / `docker compose down` |

---

## 4. Summary

- **Image**: read-only template (built from Dockerfile or pulled from a registry).
- **Container**: running instance of an image; has lifecycle and optional volumes/networks.
- **Dockerfile**: instructions to build an image; use layers and multi-stage builds wisely.
- **Registry/Repository/Tag**: where images live, which set, and which version.
- **Volume**: Docker-managed persistent storage; **bind mount**: host path mounted into container.
- **Network**: connect containers by name; **port mapping**: expose container port on the host.
- **Compose**: multi-container app defined in YAML; **health check** and **restart policy** for robustness.

Using these terms and examples consistently will make Docker predictable and easier to debug in real projects.
