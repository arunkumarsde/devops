# Chapter 1: Containerization and Virtualization

A clear explanation of virtualization vs containerization, with examples and how Docker differs from virtual machines.

---

## 1. What is Virtualization?

- **Virtualization** runs one or more **virtual machines (VMs)** on a single physical host.
- Each VM has its **own full operating system** (guest OS), kernel, libraries, and apps.
- A **hypervisor** (e.g., VMware, Hyper-V, VirtualBox) sits between the host hardware and the VMs and manages CPU, memory, and I/O.

**Point:** One physical server → many “fake” servers, each with a complete OS.

![Virtualization](/images/docker/vitualization.webp)

### Example: Virtualization

```
Physical Host (e.g., Linux)
├── Hypervisor (VMware / VirtualBox / Hyper-V)
│   ├── VM 1: Ubuntu Server 22.04  (full OS + your app)
│   ├── VM 2: Windows Server 2022  (full OS + your app)
│   └── VM 3: CentOS 8             (full OS + your app)
```

- Each VM boots its own kernel, uses its own disk image (often tens of GB), and consumes dedicated RAM and CPU.
- **Use case:** Strong isolation, different OSes on one machine, legacy apps that need a full OS.

---

## 2. What is Containerization?

- **Containerization** runs isolated **containers** that share the **host’s kernel** but have their own filesystem, process space, and network view.
- Containers do **not** run a full guest OS; they use the host OS kernel and add only the app, runtime, and libraries needed to run it.
- **Docker** is a popular platform that builds, ships, and runs containers using images and a container runtime.

**Point:** One OS kernel → many isolated “boxes” (containers), each with just what the app needs.

![Devops With Containerization](/images/docker/containerization.webp)

### Example: Containerization (Docker)

```
Physical Host (e.g., Linux)
├── Host OS kernel (shared)
├── Docker Engine
│   ├── Container 1: nginx (Alpine + nginx only)
│   ├── Container 2: Node app (node:18 + your code)
│   └── Container 3: Postgres (postgres:15 + data)
```

- Each container starts in milliseconds, uses an image that’s often hundreds of MB (or less for Alpine-based images), and shares the kernel.
- **Use case:** Microservices, CI/CD, dev/prod parity, and “run anywhere” without caring about the underlying OS (as long as it supports containers).

---

### 2.1 The Role of Hypervisors in Virtualization

A hypervisor is a layer of software that sits between the physical hardware (the host) and the virtual machines (the guests). The hypervisor allocates resources such as CPU, memory, and storage to each virtual machine. There are two types of hypervisors:

1. Type 1 Hypervisor (Bare-Metal)
   A bare-metal hypervisor is installed directly on the physical hardware (the host server). It acts as a platform for creating and running virtual machines. This type of hypervisor is used in most cloud environments, including AWS, Google Cloud, and Oracle Cloud.

**Example: VMware ESXi, Microsoft Hyper-V**

2. Type 2 Hypervisor (Hosted)
   A hosted hypervisor is installed on top of an existing operating system, such as on a personal laptop or desktop. It allows you to create virtual machines within the existing OS environment. While useful for personal use or development, this type of hypervisor is not generally used in cloud-based or enterprise environments.

**Example: Oracle VirtualBox, VMware Workstation**

### 2.2 How Virtualization Works: A Diagram

Let’s break down how virtualization works with the help of a diagram.

**Bare-Metal Hypervisor Setup (Type 1)**

![Bare-Metal Hypervisor](/images/docker/bare_metal_hypervisor.webp)

- The hypervisor is directly installed on the physical server.
- Multiple virtual machines (VMs) can run on top of it each with its own operating system and resources.

**Hosted Hypervisor Setup (Type 2)**

![Hosted Hypervisor](/images/docker/hosted_hypervisor_1.webp)

- The host OS is installed first, followed by the hypervisor.
- Virtual machines run on top of the host OS and the hypervisor.

While virtualization is powerful, it has a flaw. Every Virtual Machine requires its own full Guest Operating System.

- **Scenario:** If you run 3 VMs (one for App A, App B, and App C), you are running 3 separate Linux Kernels and 3 sets of OS libraries.
- **The Result:** Massive RAM and CPU usage just to keep the OS running, not the actual app.
  This inefficiency is exactly what Containerization (Docker) solves.

## 3. Virtualization vs Containerization — Side by Side

| Aspect           | Virtualization (VMs)          | Containerization (Docker)          |
| ---------------- | ----------------------------- | ---------------------------------- |
| **Isolation**    | Full (separate kernel per VM) | Process/filesystem/network         |
| **Guest OS**     | Full OS per VM                | No guest OS; uses host kernel      |
| **Size**         | GB per VM (OS + app)          | MB per image (app + deps only)     |
| **Boot time**    | Minutes                       | Seconds                            |
| **Resource use** | Heavier (each VM has its OS)  | Lighter (shared kernel)            |
| **Portability**  | Tied to hypervisor/VM format  | Same image runs on any Docker host |
| **Typical use**  | Multi-tenant, different OSes  | Apps, microservices, DevOps        |

**Point:** VMs virtualize **hardware**; containers virtualize the **OS** (user space) and share the kernel.

---

## 4. Docker vs Virtual Machine — Main Differences

![Docker vs. Virtual Machine](/images/docker/docker_vs_vm.webp)

### 4.1 Architecture

**Virtual Machine:**

```
App A  →  Guest OS (full)  →  Hypervisor  →  Host OS  →  Hardware
App B  →  Guest OS (full)  →  Hypervisor  →  Host OS  →  Hardware
App C  →  Guest OS (full)  →  Hypervisor  →  Host OS  →  Hardware
```

**Docker (containers):**

```
App A  →  Container (libs only)  ─┐
App B  →  Container (libs only)  ─┼→  Docker Engine  →  Host OS  →  Hardware
App C  →  Container (libs only)  ─┘
```

- **VM:** Each app runs on its own full OS and kernel.
- **Docker:** All containers use the **same** host kernel; only user space (filesystem, processes) is isolated.

### 4.2 Resource Overhead

- **VM:** Every VM needs RAM and CPU for a full OS (e.g., 1–2 GB RAM per idle VM).
- **Docker:** Containers add mostly the memory and CPU of your app; no extra full OS per container.

**Example:** On a 8 GB RAM host you might run 2–3 VMs comfortably, but dozens of small containers.

### 4.3 Speed

- **VM:** Booting a VM can take 1–5 minutes (OS boot + services).
- **Docker:** Starting a container is usually 1–3 seconds (no OS boot, just start the main process).

```bash
# Start a container (typically 1–3 seconds)
docker run -d --name web nginx:alpine
```

### 4.4 Image Size and Portability

- **VM image:** Often 10–50+ GB (full OS + tools + app).
- **Docker image:** Often 50–500 MB (base image + app + deps); Alpine-based images can be under 50 MB.

**Point:** Smaller images mean faster pull/start and easier movement across registries and hosts.

---

### How Does A Virtual Machine Work?

A piece of software called a hypervisor, or Virtual Machine manager lets you run different operating systems on different virtual machines at the same time. You can have multiple VMs, each running a different application on a different operating system.

The following Figure illustrates clearly that Hypervisor software isolating the physical Hardware on top of Host operating System, Creating different virtual machines.

![Working of VM](/images/docker/working_of_vm.png)

### How Does Docker Work?

Unlike a VM, Docker does not use hypervisor, rather it runs on top of the host operating system facilitated by a docker engine.

The following Diagram illustrates Docker provides docker client as interact mode to the User, from their it will take the respective requests through Commands execution, those Requests are evaluated in the Docker Host and provides the respective operations such as creating, pulling and pushing the images , creating containers etc.. It fetches the docker images from the Docker Registery if trying to use precreated docker images.

![Working of Docker](/images/docker/working_of_docker.png)

## 5. When to Use Which?

### Prefer Virtual Machines when:

- You need **different OS families** on the same host (e.g., Windows and Linux).
- You need **strong, kernel-level isolation** (e.g., untrusted tenants, strict compliance).
- You are running **legacy software** that expects a full OS and can’t be easily containerized.

### Prefer Docker (containers) when:

- You want **fast, repeatable deployments** and consistent dev/staging/prod.
- You are building **microservices** or modern cloud-native apps.
- You care about **density** (many apps per host) and **resource efficiency**.
- You want **“build once, run anywhere”** (same image on laptop, CI, and cloud).

---

## 6. Quick Example: Same App, VM vs Docker

### Scenario: Run a simple Nginx web server

**With a Virtual Machine:**

1. Create or download a VM image (e.g., Ubuntu 22.04) — several GB.
2. Boot the VM (1–3 minutes).
3. SSH into the VM, install Nginx, configure it, start the service.
4. Each new “instance” is another full VM (same steps, same size).

**With Docker:**

```bash
# One command; image is ~40 MB (Alpine-based), starts in seconds
docker run -d -p 8080:80 --name my-nginx nginx:alpine
```

- No guest OS to maintain; the image already has Nginx and its dependencies.
- To run 10 copies: run the same `docker run` (or use Docker Compose/Kubernetes) 10 times; each container is lightweight.

**Point:** Docker gives you a consistent, lightweight unit (image + container) instead of managing full OS instances.

---

## 7. Summary

| Concept              | Meaning                                                                                                             |
| -------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **Virtualization**   | Running full VMs with separate OSes on a hypervisor; strong isolation, higher overhead.                             |
| **Containerization** | Running isolated processes that share the host kernel; lightweight, fast, portable.                                 |
| **Docker**           | A platform for building images and running containers; not a VM — no guest OS, uses host kernel.                    |
| **Main difference**  | VMs virtualize **hardware** (full OS per VM); Docker containers virtualize **user space** and share **one kernel**. |

Use **VMs** when you need full OS isolation or different OSes; use **Docker** when you want fast, portable, resource-efficient application packaging and execution.
