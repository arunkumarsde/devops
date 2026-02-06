# Chapter 0: Docker Basics and Commands

A simple reference for everyday Docker commands: what they do and how to use them.

---

## 1. Version and System Information

### 1.1 `docker version`

- Shows **detailed version info** for both the Docker **client** (the `docker` CLI you run) and the Docker **daemon** (server/engine running on your machine).
- Useful to confirm client and server are talking and to see which versions you have.

```bash
docker version
```

**Point:** Use this when you need full version details for both client and server (e.g., for support or debugging).

---

### 1.2 `docker --version`

- Shows **only the Docker client’s version** in one short line (e.g. `Docker version 24.0.7, build ...`).
- Quick check that the `docker` command is installed and what client version you have.

```bash
docker --version
```

**Point:** Short answer to “what version of the Docker CLI do I have?”

---

### 1.3 `docker info`

- Shows **detailed information** about your Docker setup: how many images and containers you have, storage driver, kernel version, OS, memory, CPUs, and more.
- Helps you understand your Docker environment and troubleshoot issues.

```bash
docker info
```

**Point:** One command to see a broad picture of your Docker host (images, containers, kernel, OS, resources, etc.).

---

## 2. Listing Images and Containers

### 2.1 `docker images`

- **Lists all images** stored locally on your machine.
- Shows repository name, tag, image ID, size, and when it was created.

```bash
docker images
```

**Point:** “What images do I have?” → use `docker images`.

---

### 2.2 `docker ps`

- **Lists all running containers** only.
- Shows container ID, image, command, status, ports, and name.

```bash
docker ps
```

**Point:** “What containers are running right now?” → use `docker ps`.

---

### 2.3 `docker ps -a`

- **Lists all containers** — both running and stopped.
- The `-a` means “all”; without it you only see running ones.

```bash
docker ps -a
```

**Point:** “Show me every container I’ve ever created” → use `docker ps -a`.

---

## 3. Pulling Images

### 3.1 `docker pull <imagename>`

- **Downloads (pulls) an image** from a registry (default is Docker Hub).
- If you don’t specify a tag, Docker uses the **`latest`** tag.

```bash
docker pull nginx
# Same as: docker pull nginx:latest
```

**Point:** Pull = download an image to your machine so you can run containers from it.

---

### 3.2 `docker pull <imagename>:<tag>` — Specific version

- Use a **tag** to pull a **specific version** of an image instead of `latest`.

```bash
docker pull ubuntu:22.04
docker pull node:18-alpine
```

**Point:** Tag = version. `ubuntu:22.04` is a specific Ubuntu version; `node:18-alpine` is Node 18 on Alpine Linux.

---

### 3.3 Pull from a different registry

- By default, `docker pull` uses **Docker Hub**. To use another registry (e.g. AWS ECR, GCP GCR, Azure ACR, or a private registry), you **log in** to that registry, then use its **full image path**.

**Log in to a registry:**

```bash
# Log in to Docker Hub (default)
docker login

# Log in to a specific registry (e.g. private or cloud)
docker login registry.example.com
```

- **`docker login`** authenticates your Docker client with a registry so you can **push** (upload) and **pull** (download) images. You enter username and password (or token) when prompted.

**Pull from that registry:**

```bash
docker pull registry.example.com/myimage:latest
```

**Point:** `docker login` = sign in to a registry; then you can pull/push using that registry’s URL in the image name.

---

### 3.4 Pull by digest (immutable reference)

- A **digest** is a long, unique hash (e.g. `sha256:9cacb71...`) that points to **one exact build** of an image. It never changes, unlike a tag (e.g. `latest` can be updated).
- Useful when you want **exactly** the same image every time (e.g. in scripts or production).

```bash
docker pull alpine@sha256:9cacb71397b640eca97488cf08582ae4e4068513101088e9f96c9814bfda95e0
```

**Point:** Tag can change; digest does not. Use digest when you need a fixed, immutable image reference.

---

## 4. Pushing Images

### 4.1 `docker push`

- **Uploads (pushes) an image** from your machine to a registry (e.g. Docker Hub or a private registry).
- You must be **logged in** (`docker login`) and usually **tag** the image with the registry and your username so the push goes to the right place.

```bash
docker tag myapp username/myapp:v1
docker push username/myapp:v1
```

**Point:** Pull = download; push = upload. Push sends your image to a registry so others (or other machines) can pull it.

---

## 5. Removing Images

### 5.1 `docker rmi <imagename>`

- **Removes (deletes) an image** from your local machine.
- If any container (running or stopped) still uses that image, Docker will complain unless you force remove the container first or use `-f` (force) on the image.

```bash
docker rmi nginx
docker rmi nginx:alpine
```

**Point:** `docker rmi` = remove image. Free disk space when you no longer need an image.

---

## 6. Running Containers

### 6.1 `docker run <image>`

- **Creates and starts a new container** from an image. If the image isn’t local, Docker pulls it first.
- Uses the image’s default command (e.g. for `ubuntu`, the default might just exit unless you give a command).

```bash
docker run ubuntu
# Container may start and exit immediately if there's no long-running process
```

**Point:** `docker run` = “create a new container from this image and start it.”

---

### 6.2 `docker run <image>` vs `docker run <container_id>`

- **`docker run <image>`** — Creates a **brand new** container from the image (pull if needed, create, then run).
- **`docker run <container_id>`** is **not** the usual way to start a container. To start an **existing** (stopped) container, you use **`docker start <container_id>`** (see below).

**Point:** New container = `docker run <image>`. Existing stopped container = `docker start <container_id>`.

---

### 6.3 `docker run -it <image> bash`

- **`-i`** = keep STDIN open (interactive).
- **`-t`** = allocate a pseudo-TTY (like a terminal).
- Together **`-it`** lets you **enter the container** and use a shell (e.g. `bash` or `sh`) as if you were logged in.

```bash
docker run -it ubuntu bash
# You're now inside the container; type 'exit' to leave
```

**Point:** `-it` = “run this container and give me an interactive shell inside it.”

---

### 6.4 `docker run -d <image>`

- **`-d`** = **detached**. The container runs in the **background**; your terminal is free and the container keeps running.

```bash
docker run -d nginx
```

**Point:** `-d` = run in background so you get your prompt back while the container keeps running.

---

### 6.5 `docker run --name <name> -d <image>`

- **`--name`** gives the container a **friendly name** so you can refer to it as `my-nginx` instead of a long ID.

```bash
docker run --name my-nginx -d nginx
docker stop my-nginx
docker start my-nginx
```

**Point:** Names make it easier to manage containers (`docker logs my-nginx`, `docker exec my-nginx ...`).

---

### 6.6 `docker run -p 8080:80 <image>`

- **`-p 8080:80`** = **publish port**: map **host port 8080** to **container port 80**.
- Traffic to `http://localhost:8080` on your machine goes to port 80 inside the container.

```bash
docker run -p 8080:80 nginx
# Access the app at http://localhost:8080
```

**Point:** `-p HOST_PORT:CONTAINER_PORT` = “send host traffic on this port to that port in the container.”

---

## 7. Stopping, Starting, and Restarting Containers

### 7.1 `docker stop <container_id>`

- **Stops a running container** gracefully (sends SIGTERM, then SIGKILL if needed). The container still exists but is in “exited” state.

```bash
docker stop my-nginx
```

**Point:** Stop = container no longer runs but is still there; you can start it again with `docker start`.

---

### 7.2 `docker start <container_id>`

- **Starts a stopped container** again. It’s the **same** container (same ID, same filesystem), not a new one.

```bash
docker start my-nginx
```

**Point:** Start = “turn this existing container back on.”

---

### 7.3 `docker restart <container_id>`

- **Stops** the container and then **starts** it again. Same container, fresh process.

```bash
docker restart my-nginx
```

**Point:** Restart = stop + start in one command.

---

### 7.4 Difference: `docker run` vs `docker start` vs `docker restart`

| Command | What it does |
|--------|----------------|
| **`docker run <image>`** | **Pulls** (if needed), **creates** a **new** container, and **runs** it. Every time you run this, you get a new container. |
| **`docker start <container_id>`** | **Starts** an **existing stopped** container. No pull, no new container. |
| **`docker restart <container_id>`** | **Stops** an existing container and then **starts** it again. Same container. |

**Point:** `run` = new container; `start` = resume a stopped one; `restart` = stop then start the same one.

---

## 8. Removing Containers

### 8.1 `docker rm <container_id>`

- **Removes (deletes) a container** from your machine. The container must be **stopped** first (or use `-f` to force).

```bash
docker stop my-nginx
docker rm my-nginx
```

**Point:** `docker rm` = delete a container. It must be stopped unless you use `-f`.

---

### 8.2 `docker rm -f <container_id>`

- **Force remove**: stops the container if it’s running and then removes it in one step.

```bash
docker rm -f my-nginx
```

**Point:** `-f` = “stop it if running and remove it anyway.”

---

## 9. Logs and Inspecting Containers

### 9.1 `docker logs <container_id>`

- **Shows the logs** (stdout/stderr) of a container. You see what the main process inside the container has printed.

```bash
docker logs my-nginx
```

**Point:** “What did this container print?” → use `docker logs`.

---

### 9.2 `docker logs -f <container_id>`

- **`-f`** = **follow**. Logs stream in real time (like `tail -f`). Press Ctrl+C to stop following.

```bash
docker logs -f my-nginx
```

**Point:** `-f` = watch logs live as they are produced.

---

### 9.3 `docker inspect <container_id>`

- **Shows detailed low-level info** about a container (or image): config, network, mounts, state, IP, env vars, etc. Output is JSON by default.

```bash
docker inspect my-nginx
```

**Point:** “Show me everything about this container” → use `docker inspect`.

---

## 10. Running Commands Inside a Running Container

### 10.1 `docker exec -it <container_id> bash`

- **Runs a command inside an already-running container.**  
- **`-it`** = interactive + TTY, so you get a **shell** you can type in (e.g. `bash` or `sh`).

```bash
docker exec -it my-nginx bash
# You're inside the container; exit when done
```

**Point:** “Open a shell inside this running container” → `docker exec -it <container> bash` (or `sh` if bash isn’t available).

---

## 11. Cleaning Up (Prune)

### 11.1 `docker container prune`

- **Removes all stopped containers.** Running containers are left alone.

```bash
docker container prune
```

**Point:** Quick way to delete every stopped container and free some space.

---

### 11.2 `docker image prune`

- **Removes unused images** (images not used by any container). Often used after `container prune` to free more space.

```bash
docker image prune
```

**Point:** Removes images that nothing is using.

---

### 11.3 `docker system prune`

- **Removes “everything” unused**: stopped containers, unused networks, dangling images, and optionally build cache. Use with care.

```bash
docker system prune
# Add -a to remove all unused images (not just dangling)
docker system prune -a
```

**Point:** Big cleanup: containers, networks, and images (and with `-a`, all unused images). Read the prompt before confirming.

---

## 12. Building Images

### 12.1 `docker build -t myapp .`

- **Builds an image** from a **Dockerfile** in the current directory (`.`).
- **`-t myapp`** = **tag** the built image as `myapp` (and optionally a tag like `myapp:v1` with `-t myapp:v1`).

```bash
docker build -t myapp .
docker build -t myapp:v1 .
```

**Point:** Build = turn a Dockerfile into an image; `-t` gives that image a name (and optional version tag).

---

## 13. Tagging and Pushing Your Image

- Before pushing to a registry, you usually **tag** your image with the registry and name the registry expects (e.g. `username/imagename:tag` for Docker Hub).

```bash
# Tag your local image for Docker Hub
docker tag myapp username/myapp:v1

# Push to Docker Hub (after docker login)
docker push username/myapp:v1
```

**Point:** Tag = give the image a full name (including registry/username); push = upload it to that registry.

---

## 14. Networks and Volumes (Listing)

### 14.1 `docker network ls`

- **Lists all Docker networks** on your machine (bridge, host, none, and any custom networks).

```bash
docker network ls
```

**Point:** See which networks exist for connecting containers.

---

### 14.2 `docker volume ls`

- **Lists all Docker volumes** (persistent storage that containers can use).

```bash
docker volume ls
```

**Point:** See which volumes exist for storing data outside containers.

---

## 15. Image History and Container Stats

### 15.1 `docker history <image_id>`

- **Shows the layers (history)** of an image: each Dockerfile instruction that created a layer, plus size. Helps you see how the image was built and why it’s big.

```bash
docker history nginx:alpine
docker history myapp
```

**Point:** “How was this image built and what’s in each layer?” → use `docker history`.

---

### 15.2 `docker stats`

- **Shows live resource usage** for your running containers. Updates in real time until you press Ctrl+C.
- Typically shows:
  - **CPU %** — CPU usage
  - **Memory usage / limit** — RAM used and limit (if set)
  - **Network I/O** — bytes in/out
  - **Block I/O** — disk read/write

```bash
docker stats
# Or for specific containers:
docker stats my-nginx my-postgres
```

**Point:** “How much CPU, memory, and I/O are my containers using?” → use `docker stats`.

---

## 16. Quick Reference Table

| Goal | Command |
|------|--------|
| Docker client version | `docker --version` |
| Full version (client + server) | `docker version` |
| System/engine details | `docker info` |
| List images | `docker images` |
| List running containers | `docker ps` |
| List all containers | `docker ps -a` |
| Pull image (latest) | `docker pull <imagename>` |
| Pull specific version | `docker pull <imagename>:<tag>` |
| Log in to registry | `docker login` or `docker login registry.example.com` |
| Push image | `docker push username/myapp:v1` |
| Remove image | `docker rmi <imagename>` |
| Run new container | `docker run [OPTIONS] <image>` |
| Run in background | `docker run -d <image>` |
| Run with name | `docker run --name my-nginx -d <image>` |
| Run with port map | `docker run -p 8080:80 <image>` |
| Interactive shell | `docker run -it <image> bash` |
| Stop container | `docker stop <container_id>` |
| Start stopped container | `docker start <container_id>` |
| Restart container | `docker restart <container_id>` |
| Remove container | `docker rm <container_id>` or `docker rm -f <container_id>` |
| View logs | `docker logs <container_id>` or `docker logs -f <container_id>` |
| Inspect container | `docker inspect <container_id>` |
| Shell in running container | `docker exec -it <container_id> bash` |
| Remove stopped containers | `docker container prune` |
| Remove unused images | `docker image prune` |
| Big cleanup | `docker system prune` |
| Build image | `docker build -t myapp .` |
| Tag image | `docker tag myapp username/myapp:v1` |
| List networks | `docker network ls` |
| List volumes | `docker volume ls` |
| Image layer history | `docker history <image_id>` |
| Live container stats | `docker stats` |

---

**Point:** Use this chapter as a quick lookup for what each command does in simple terms.
