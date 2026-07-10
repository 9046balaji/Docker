<div align="center">
  <h1>🌐 Docker Networking Fundamentals</h1>
  <p><strong>Understanding Docker networks, bridge drivers, DNS resolution, and port mapping</strong></p>
  <img src="https://img.shields.io/badge/Level-Intermediate-yellow?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-10%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Section-06%20of%2011-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [What Is a Docker Network?](#-what-is-a-docker-network)
2. [Creating a Network](#-creating-a-network)
3. [Bridge Driver](#-bridge-driver)
4. [Docker Internal DNS](#-docker-internal-dns)
5. [Network Modes](#-network-modes)
6. [Port Mapping vs Internal Networking](#-port-mapping-vs-internal-networking)
7. [IPAM (IP Address Management)](#-ipam-ip-address-management)
8. [Connecting Containers Across Networks](#-connecting-containers-across-networks)

---

## 🤔 What Is a Docker Network?

A Docker network is a virtual private network created by Docker that enables container communication. It provides:

* **Inter-container communication** — Containers on the same network can talk to each other
* **Traffic isolation** — Containers on different networks are isolated by default
* **Service exposure** — Containers can expose ports to the host machine
* **DNS-based discovery** — Containers can find each other by name instead of IP address

```text
 _______________________________________________
|           DOCKER NETWORK OVERVIEW             |
|                                               |
|   Without Network:                            |
|   [ Container A ]    [ Container B ]          |
|        (isolated - cannot communicate)        |
|                                               |
|   With Network:                               |
|   [ Container A ] <--mynet--> [ Container B ] |
|        (connected - can communicate)          |
|_______________________________________________|
```

---

## 🔧 Creating a Network

```bash
docker network create mynet
```

When Docker creates a network, it provisions:

* A virtual bridge switch
* A subnet (e.g., `172.18.0.0/16`)
* A gateway (e.g., `172.18.0.1`)
* An internal DNS system
* Internal routing rules

---

## 🌉 Bridge Driver

Docker uses a **bridge** driver by default, which creates a virtual Ethernet bridge — similar to a virtual LAN switch.

Containers attached to the same bridge network:

* Can communicate with each other directly
* Receive private IP addresses from the subnet
* Can resolve each other's names through Docker DNS

> [!IMPORTANT]
> Containers on **different** bridge networks **cannot** communicate directly. This provides network isolation by default. To allow cross-network communication, you must explicitly connect a container to the other network.

---

## 🔠 Docker Internal DNS

Docker provides an internal DNS server that allows containers to use container names as hostnames for communication.

**Example:**

```bash
docker run --name db --network mynet postgres
docker run --name app --network mynet ubuntu
```

**Inside the `app` container:**

```bash
ping db    # Works automatically — Docker DNS resolves "db" to its IP address
```

```text
 ___________________________________________________________
|              DOCKER INTERNAL DNS RESOLUTION                |
|                                                           |
|   [ app container ]  -- ping db -->  [ DNS Server ]       |
|                                          |                |
|                                          v                |
|                                   Resolves "db" to       |
|                                   172.18.0.2             |
|                                          |                |
|                                          v                |
|                                   [ db container ]       |
|___________________________________________________________|
```

---

## 🔒 Network Modes

Docker supports three network modes, each providing a different level of isolation:

### None Network (Complete Isolation)

```bash
docker run --network none ubuntu
```

The container receives:
* No internet access
* No network interface (except loopback)
* **Complete network isolation**

> [!NOTE]
> Use this mode for security-sensitive workloads that should never communicate over a network.

---

### Host Network (No Isolation)

```bash
docker run --network host nginx
```

The container shares the host machine's network stack directly:
* No network isolation between the container and host
* The container uses the host's ports and interfaces directly
* No port mapping is needed (`-p` flag is unnecessary)

> [!WARNING]
> Host networking removes the network isolation boundary. The container can access all network interfaces and ports on the host machine. Use with caution.

---

### Bridge Network (Default — Controlled Isolation)

```bash
docker run --network mynet nginx
```

The default mode. Containers get their own private network with controlled access to each other and the outside world.

---

## 🔌 Port Mapping vs Internal Networking

### Internal Container Communication

Containers on the same network communicate using:
* Docker network
* Container IP addresses
* Container DNS names (container names)

*Example:* `backend` container connects to `postgres` container by name.

### External Host Access

External access from the host machine or a browser requires port mapping:

```bash
docker run -d -p 8080:80 nginx
```

* Host port `8080` → Container port `80`

```text
 ___________________________________________________________
|              COMPLETE REQUEST FLOW                        |
|                                                           |
|   Browser --> localhost:8080 --> Docker port mapping       |
|                                      |                    |
|                                      v                    |
|                               [ nginx container ]         |
|                                      |                    |
|                                      v                    |
|                               [ backend container ]       |
|                                      |                    |
|                                      v                    |
|                               [ postgres container ]      |
|___________________________________________________________|
```

---

## 📊 IPAM (IP Address Management)

Docker automatically manages IP address allocation for containers:

* **Subnet allocation** — Each network gets its own subnet range
* **IP assignment** — Each container receives a unique IP within the subnet
* **Gateway configuration** — A gateway is created for external routing

---

## 🔗 Connecting Containers Across Networks

Containers on different bridge networks are isolated by default. To allow a container to communicate with containers on another network, connect it explicitly:

```bash
docker run -d \
  --name nginx \
  --network login-app-default \
  -p 80:80 \
  -v ${PWD}/nginx.conf:/etc/nginx/nginx.conf \
  nginx
```

| Option | Purpose |
|--------|---------|
| `--network login-app-default` | Connects the nginx container to the `login-app-default` bridge network, enabling communication with containers on that network |
| `-p 80:80` | Maps host port 80 to container port 80 for browser access |
| `-v ${PWD}/nginx.conf:/etc/nginx/nginx.conf` | Mounts a custom nginx configuration file from the host into the container |

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Docker Compose](./05-Docker-Compose.md) | [README](../README.md) | [Cross-Network Communication](./07-Cross-Network-Communication.md) |

</div>
