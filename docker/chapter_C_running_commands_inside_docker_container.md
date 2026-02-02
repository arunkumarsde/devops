# Chapter 2: Running Commands Inside a Docker Container

How to run one-off commands, override the default command, and execute commands in a running container.

---

## 1. Overview

- You can run commands inside a container in two main ways:
  1. **At container start** — pass a command to `docker run` (overrides the image’s `CMD`).
  2. **In a running container** — use `docker exec` to run a command in an already-running container.
- Commands can be **interactive** (e.g., a shell you type in) or **non-interactive** (run and exit).

**Point:** `docker run <command>` = “start a container and run this command”; `docker exec <container> <command>` = “run this command inside a container that’s already running.”

---

## 2. Running a Command When the Container Starts (`docker run`)

- By default, `docker run <image>` runs the image’s **CMD** (or **ENTRYPOINT**). You can override it by appending a command and its arguments.

**Syntax:**

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

**Examples:**

```bash
# Run a one-off command: start container, run echo, then container exits
docker run --rm alpine echo "Hello, World"

# Override CMD: run ls instead of the default nginx process
docker run --rm nginx:alpine ls -la /usr/share/nginx/html

# Run a different program (e.g., shell)
docker run --rm alpine sh -c "echo hello && whoami"
```

- If the command you pass is the main process and it exits, the container stops. Use `-d` to run the image’s default process in the background, then use `docker exec` for extra commands.

**Point:** Whatever you put after the image name replaces (or is passed to) the image’s default command for that run.

---

## 3. Running a Command in a Running Container (`docker exec`)

- **`docker exec`** runs a command inside a container that is **already running**. The container must be in the “running” state.

**Syntax:**

```bash
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

**Examples:**

```bash
# Start a long-running container in the background
docker run -d --name my-nginx nginx:alpine

# Run a one-off command inside it (non-interactive)
docker exec my-nginx cat /etc/nginx/nginx.conf

# Run ls in the container
docker exec my-nginx ls -la /usr/share/nginx/html

# Run a shell command
docker exec my-nginx sh -c "echo hello from container"
```

- The container keeps running after `docker exec` finishes (unless the command you run kills the main process).

**Point:** Use `docker exec` to debug, inspect files, or run admin tasks inside a live container without restarting it.

---

## 4. Interactive Shell Inside a Container

- To **open a shell** and type commands interactively, use **`-i`** (interactive) and **`-t`** (allocate a pseudo-TTY). Combined: **`-it`**.

**With `docker run` (new container + shell):**

```bash
# Start a new container and open a shell (container exits when you exit the shell)
docker run -it --rm alpine sh
```

- Inside the container you can run `ls`, `cat`, `ps`, etc. Type `exit` (or Ctrl+D) to leave and stop the container. `--rm` removes the container when it exits.

**With `docker exec` (shell in an already-running container):**

```bash
# Open a shell in the running nginx container
docker exec -it my-nginx sh
```

- After you `exit`, the container (e.g., nginx) keeps running.

**Point:** Use `-it` when you want an interactive shell; use `-i` only if you’re piping input and don’t need a TTY.

---

## 5. Common Options for `docker run` and `docker exec`

| Option   | Meaning                            | Example use                            |
| -------- | ---------------------------------- | -------------------------------------- |
| `-it`    | Interactive + TTY (for shells)     | `docker run -it alpine sh`             |
| `-d`     | Detached (run in background)       | `docker run -d nginx:alpine`           |
| `--rm`   | Remove container when it exits     | `docker run --rm alpine echo hi`       |
| `--name` | Give container a name              | `docker run -d --name web nginx`       |
| `-e`     | Set environment variable           | `docker run -e MYVAR=value alpine env` |
| `-w`     | Working directory inside container | `docker exec -w /app my-nginx pwd`     |
| `-u`     | Run as user (name or UID)          | `docker exec -u root my-nginx whoami`  |

**Examples:**

```bash
# Run with env var
docker run --rm -e USER=guest alpine env | grep USER

# Run command in a specific working directory
docker exec -w /usr/share/nginx/html my-nginx pwd

# Run as root inside the container (e.g., install a package)
docker exec -u root -it my-nginx sh
```

**Point:** `-it` for interactive shell; `--rm` for one-off runs; `-e` / `-w` / `-u` to control environment, directory, and user.

---

## 6. Shell Form vs Exec Form

- Commands can be specified in **shell form** (via a shell) or **exec form** (direct executable, no shell).

**Shell form** (uses `/bin/sh -c "..."`):

```bash
docker run --rm alpine echo "Hello"
docker run --rm alpine sh -c "ls -la && pwd"
```

- Good for pipes, `&&`, variables; subject to shell parsing and signal handling.

**Exec form** (no shell, direct exec):

```bash
docker run --rm alpine ls -la
docker exec my-nginx cat /etc/nginx/nginx.conf
```

- No shell expansion; first token must be the executable. Prefer this in Dockerfiles (`CMD ["node", "server.js"]`) and when you don’t need shell features.

**Point:** Use shell form when you need `&&`, pipes, or env expansion; use exec form for clarity and proper signal handling.

---

## 7. One-Off Commands vs Long-Running Containers

**One-off command** — run something and let the container exit:

```bash
docker run --rm alpine date
docker run --rm node:18-alpine node -e "console.log(1+1)"
```

- `--rm` avoids leaving stopped containers behind.

**Long-running container** — start the app in the background, then run extra commands with `exec`:

```bash
docker run -d --name db postgres:15-alpine
docker exec db psql -U postgres -c "SELECT version();"
docker exec -it db psql -U postgres   # interactive SQL
```

**Point:** One-off = `docker run --rm <image> <command>`; long-running = `docker run -d`, then `docker exec` for ad-hoc commands.

---

## 8. Practical Use Cases

### 8.1 Inspect files or config

```bash
docker exec my-nginx cat /etc/nginx/nginx.conf
docker exec my-nginx ls -la /usr/share/nginx/html
```

### 8.2 Debug / install tools in a running container

```bash
docker exec -u root -it my-nginx sh
# inside: apk add curl && curl -I http://localhost
```

### 8.3 Run a script or command as a different user

```bash
docker exec -u nobody my-nginx whoami
docker exec -w /var/www my-nginx pwd
```

### 8.4 Run database CLI or one-off SQL

```bash
docker exec -it my-postgres psql -U postgres -d mydb
docker exec my-postgres psql -U postgres -c "SELECT 1"
```

### 8.5 Check processes or resources

```bash
docker exec my-nginx ps aux
docker exec my-nginx top
```

**Point:** `docker exec` is your main tool for debugging and admin inside a running container without changing the image or restarting.

---

## 9. Summary

| Goal                              | Command pattern                                    |
| --------------------------------- | -------------------------------------------------- |
| Run one command at start          | `docker run [OPTIONS] IMAGE COMMAND [ARG...]`      |
| Run command in running container  | `docker exec [OPTIONS] CONTAINER COMMAND [ARG...]` |
| Interactive shell (new container) | `docker run -it --rm IMAGE sh`                     |
| Interactive shell (running)       | `docker exec -it CONTAINER sh`                     |
| One-off, auto-remove              | `docker run --rm IMAGE COMMAND`                    |

**Point:** Use `docker run` to start a container and optionally override its command; use `docker exec` to run commands inside a container that’s already running.
