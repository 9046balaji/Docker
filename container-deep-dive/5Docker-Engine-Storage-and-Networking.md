# Docker Engine, Storage, and Networking

## Table of Contents
- [What is Docker?](#what-is-docker)
- [Docker Lifecycle](#docker-lifecycle)
- [Dockerfile](#dockerfile)
- [Container State Lifecycle](#container-state-lifecycle-diagram)
- [Docker Architecture](#understanding-terminology)
- [Docker Storage: Bind Mounts and Volumes](#docker-bind-mounts-and-volumes)
- [Docker Networking](#docker-networking)
- [Network Types and Container Isolation](#docker-networking-types--container-isolation)

---

## What is Docker?

Docker is a containerization platform that provides an easy way to containerize your applications. This means that by using Docker, you can build container images, run those images to create containers, and also push these images to container registries such as Docker Hub, Quay.io, and so on.

In simple words, containerization is a concept or technology, and Docker is the tool that implements containerization.

---

## Docker Lifecycle

There are three important steps in the Docker lifecycle:

1. **`docker build`** → Builds Docker images from a Dockerfile.
2. **`docker run`** → Runs containers from Docker images.
3. **`docker push`** → Pushes the container image to public or private registries to share the Docker image.

---

## Dockerfile

A Dockerfile is a set of instructions used to build an image.

**Example:** Get an Ubuntu image, install application libraries, copy code, etc.

---

## Container State Lifecycle Diagram

```text
                       .------------.
                       |   Paused   |
                       '---^----|---'
                    pause  |    |  unpause
                           |    v
     .--------.  create  .-----------.  start  .------------.
    |  Image  |--------->|  created  |--------->|  Running   |
     '---|----'          '-----------'         |  /Active   |
         |                                     '---^----|---'
         |                    run                  |    |
         '-----------------------------------------'    | stop / kill
                                               start    v
                                               .------------.    rm    .-----------.
                                               |  stopped   |--------->|  deleted  |
                                               '------------'          '-----------'
```

---

## Understanding Terminology

### Docker Daemon

The Docker daemon (`dockerd`) listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes. A daemon can also communicate with other daemons to manage Docker services.

> **Note:** A daemon is simply a background service in Linux.

### Docker Registries

A Docker registry stores Docker images. Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default. You can also run your own private registry.

```text
     [ Client / CLI ]
    (pull / run / push)
            |
            | (API Requests)
            v
 ___________________________________________________________
|                       DOCKER DAEMON                       |
|        (Manages Containers, Images, Networks, Volumes)    |
|___________________________________________________________|
            ^                                       |
            | (docker pull / run)                   | (docker push)
            | (Downloads Image)                     | (Uploads Image)
            |                                       v
 ___________________________________________________________
|                      DOCKER REGISTRY                      |
|               (Docker Hub / Private Registry)             |
|___________________________________________________________|
```

When you use the `docker pull` or `docker run` commands, the required images are pulled from your configured registry. When you use the `docker push` command, your image is pushed and saved to your configured registry.

---

## Docker Bind Mounts and Volumes

### (1) Bind Mounts

Bind mounts allow you to bind any folder or file on your host computer directly into a container. You specify an existing host path and a destination container path so the container can see and modify those host files instantly.

```text
 ___________________________________________________________
|                        HOST COMPUTER                      |
|                                                           |
|   [ Host File System ]                                    |
|   └── /path/on/host (e.g., /app)                          |
|           |                                               |
|           | ===============( Bind Mount )==============.  |
|           v                                            |  |
|    _______________________________________________     |  |
|   |             CONTAINER (C1)                    |    |  |
|   |                                               |    |  |
|   |   └── /app/inside/container <______________________'  |
|   |_______________________________________________|       |
|___________________________________________________________|
```

### (2) Volumes

Volumes are persistent data stores created and completely managed by Docker. They are stored safely under Docker's own dedicated storage directory on the host machine.

```text
 ___________________________________________________________
|                        HOST COMPUTER                      |
|                                                           |
|   [ Docker Managed Storage ]                              |
|   └── /var/lib/docker/volumes/my_vol/_data                |
|           |                                               |
|           | ==================( Volume )===============.  |
|           v                                            |  |
|    _______________________________________________     |  |
|   |             CONTAINER (C1)                    |    |  |
|   |                                               |    |  |
|   |   └── /app/data <__________________________________'  |
|   |_______________________________________________|       |
|___________________________________________________________|
```

**Key Benefits of Volumes:**
- **External Storage Support:** We can create volumes using external cloud storage sources (unlike bind mounts), and we can manage them completely through lifecycle actions (e.g., creating, destroying, etc.).
- **Control:** They can be fully controlled using the Docker CLI or Docker API.
- **Safety:** They are much safer and cleaner for sharing data across multiple running containers simultaneously.
- **Portability:** Volumes are highly portable and easier to maintain compared to traditional bind mounts.

---

## Docker Networking

At its core, Docker networking is the system that allows Docker containers to communicate with each other, with the Docker host, and with the outside world.

When you create a container, Docker automatically gives it its own **isolated network environment**. This means each container gets its own private IP address and network stack. By default, containers running on the same host can communicate with each other directly without needing to expose ports to the host machine, creating a highly secure virtual network.

**Core Networking Scenarios:**
- There are times when one container needs to talk to another container (e.g., Frontend talking to a Backend API).
- There are scenarios where a container should be completely isolated from other containers for security.
- By default, a container needs a way to communicate with the host machine because containers are lightweight and do not have a complete OS or physical network adapters.

```text
 ___________________________________________________________
|                        HOST / EC2                         |
|  =======================================================  |
|                         DOCKER                            |
|  =======================================================  |
|   .-------------------.           .-------------------.   |
|  |   Frontend (C1)   |<=========>|   Backend (C2)    |   |
|   '-------------------'           '-------------------'   |
|___________________________________________________________|
```

---

### Container ↔ Host: Bridge Networking

Bridge networking is the default network driver used when you run a container. It creates a software-based virtual bridge on the host machine (usually named `docker0`), which acts like a virtual switch connecting the host network to the container's private network layer.

```text
 ___________________________________________________________
|                         HOST OS                           |
|                                                           |
|   [ Host Physical Interface: eth0 ] (IP: 192.168.3.4)     |
|                ^                                          |
|                |                                          |
|   [ Docker Bridge Switch: docker0 ]                       |
|                ^                                          |
|                | (veth pair link)                         |
|                v                                          |
|    _______________________________________________        |
|   |             CONTAINER (APP RUNTIME)           |       |
|   |                                               |       |
|   |   [ Container Interface: eth0 ]               |       |
|   |   (Private IP: 172.17.4.5)                    |       |
|   |_______________________________________________|       |
|___________________________________________________________|
```

> **CRITICAL NOTE:** Without the `docker0` bridge acting as a translator, a network error occurs because the container app cannot directly reach out to the host's physical network adapter.

#### How it works under the hood:

- **`veth` (Virtual Ethernet):** Docker creates a virtual wire pair (`veth`). One end of the wire hooks into the container's internal interface (`eth0`), and the other end attaches directly to the host's `docker0` bridge.
- **Traffic Routing:** When the application inside the container transmits data, it goes through `veth` to the `docker0` bridge, which securely routes the traffic out through the host's physical network card (`eth0`).

---

## Docker Networking Types & Container Isolation

Here is the structured breakdown of Docker network drivers and container-to-container isolation boundaries.

### (1) Default Bridge Networking

By default, whenever you spin up a container without specifying a network, Docker automatically attaches it to the default built-in **bridge network** (driven by the `docker0` interface).

### (2) Host Networking

In this mode, Docker completely removes network isolation between the container and the host machine. The container binds directly to the host's network interfaces (`veth`).

```text
 ___________________________________________________________
|                        HOST MACHINE                       |
|   Shared Subnet: 192.168.3.X                              |
|                                                           |
|   [ Host OS IP: 192.168.3.4 ]                             |
|               ||                                          |
|               || (Bypasses network isolation completely)  |
|               \/                                          |
|   [ Container IP: 192.168.3.6 ] (Shares host ports)       |
|___________________________________________________________|
```

> ⚠️ **CRITICAL NOTE:** **This is insecure.** Because the container shares the exact same network stack as the host, any vulnerability inside the container gives direct exposure to the host's open ports and local network subnet.

### (3) Overlay Networking

This driver is used when you need to establish a secure cluster **across multiple physical or virtual hosts**. It acts as the backbone layer for container orchestration engines like **Kubernetes (K8s)** and **Docker Swarm**.

```text
  [ Physical/VM Host 1 ]                [ Physical/VM Host 2 ]
 .----------------------.              .----------------------.
|    [ Container C1 ]    |            |    [ Container C2 ]    |
 '----------+-----------'              '----------+-----------'
            |                                     |
 ===========|=====================================|===========
            v                                     v
  ___________________________________________________________
 |                 OVERLAY VIRTUAL NETWORK                   |
 |         (Secure tunnel clustering across hosts)           |
 |___________________________________________________________|
```

**Use Case:** If there are multiple hosts, an overlay network safely encapsulates and tunnels traffic between containers on different machines so they can communicate as if they were sitting on the same local switch.

### Container-to-Container Isolation (The Security Problem)

If you rely strictly on the default out-of-the-box bridge network (`docker0`), you run into a massive security isolation loophole.

```text
 ___________________________________________________________
|                     DEFAULT BRIDGE SYSTEM                 |
|                                                           |
|    [ Container C1: Login ]       [ Container C2: Payments]|
|          (Compromised!)          |       (Target)         |
|                 \                |               /        |
|                  v               v              v         |
|             .----------------------------------------.    |
|            |      Shared Default Bridge (docker0)     |   |
|             '----------------------------------------'    |
|                                 ^                         |
|                                 |                         |
|                           (HACKER PATH)                   |
|___________________________________________________________|
```

**The Flaw:** By default, every container hooked into the default `docker0` bridge can see, discover, and `ping` every other container on that same bridge using their internal `veth` paths. If a hacker compromises your exposed **Login Container (C1)**, they can freely traverse the internal bridge network to sniff or attack your private **Payments Container (C2)**.

### The Solution: Custom User-Defined Bridge Networks

Docker elegantly solves this issue by letting you provision your own **custom bridge networks** to enforce strict multi-tenant boundary isolation.

```text
 ___________________________________________________________
|               MUTUAL ISOLATION VIA CUSTOM BRIDGES         |
|                                                           |
|   [ Container C1: Login ]       [ Container C2: Payments] |
|              |                             |              |
|              v                             v              |
|     .-----------------.           .-----------------.     |
|    |  Custom Bridge A |          |  Custom Bridge B |     |
|    |  (login-net)     |          |  (payment-net)   |     |
|     '-----------------'           '-----------------'     |
|              \                             /              |
|               '-------------.-------------'               |
|                             |                             |
|                             v                             |
|                 [ Host Operating System ]                 |
|___________________________________________________________|
```

**How it works:** By creating separate custom bridge networks (e.g., `login-net` and `payment-net`), containers on different networks cannot communicate with each other by default. This provides complete, zero-trust network isolation between different app tiers right inside the host's virtual networking engine itself.

### Default Bridge Network Allocation

This diagram illustrates how Docker automatically assigns IP addresses to multiple containers running on a single host using the default bridge interface (`docker0`).

```text
 ___________________________________________________________
|                         DOCKER HOST                       |
|                                                           |
|    [ Container C1 ]                           [ Container C2 ]  |
|    IP: 172.17.0.2                             IP: 172.17.0.3    |
|          \                                          /       |
|           \                                        /        |
|            v                                      v         |
|         .--------------------------------------------.      |
|        |          Default Bridge (docker0)           |      |
|         '--------------------------------------------'      |
|            ^                                      ^         |
|           /                                        \        |
|          /                                          \       |
|    [ Container C3 ]                           [ Container C4 ]  |
|    IP: 172.17.0.4                             IP: 172.17.0.5    |
|___________________________________________________________|
```

#### Key Concepts:

- **Central Virtual Switch (`docker0`):** Whenever containers are spun up on the host without a custom network configuration, they are instantly hooked into this shared default network bridge.
- **Sequential IP Assignment:** Docker running as a local network coordinator automatically allocates unique, isolated IP addresses within the same subnet pool (typically starting from `172.17.0.2` onwards) to each sequential container instance (`C1` → `C2` → `C3` → `C4`).
- **Inter-Container Communication:** Because all four containers reside on the exact same default network subnet via `docker0`, they can freely discover and ping each other out-of-the-box using these internal IP addresses.

---

## Summary: Docker Storage and Networking

This document covered the essential components of Docker's architecture:

**Storage Options:**
- **Bind Mounts:** Direct host-to-container file sharing
- **Volumes:** Docker-managed persistent storage with better security and portability

**Networking Types:**
- **Bridge Networking:** Default isolated container networking via `docker0`
- **Host Networking:** Direct host network access (less secure)
- **Overlay Networking:** Multi-host container communication
- **Custom Networks:** Enhanced security through network isolation

**Key Security Principle:** Always use custom bridge networks in production to prevent unauthorized container-to-container communication and maintain proper security boundaries.

