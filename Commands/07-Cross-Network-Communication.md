<div align="center">
  <h1>🔗 Cross-Network Container Communication</h1>
  <p><strong>How to connect containers across different Docker bridge networks using multi-network attachment</strong></p>
  <img src="https://img.shields.io/badge/Level-Intermediate-yellow?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-6%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Section-07%20of%2011-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [The Problem: Network Isolation](#-the-problem-network-isolation)
2. [The Solution: Multi-Network Attachment](#-the-solution-multi-network-attachment)
3. [Practical Example: Creating a Shared Network](#-practical-example-creating-a-shared-network)

---

## ❌ The Problem: Network Isolation

When containers are on different bridge networks, they cannot communicate with each other. This is by design — Docker bridge networks provide isolation.

**Scenario:** An `nginx` reverse proxy needs to route traffic to a `book-management-backend` service, but they are on separate bridge networks:

* `nginx` is on the `login-app-default` network
* `book-management-backend` is on the `spring-react-book-network`

```text
 _____________________________________________________________
|                   NETWORK ISOLATION                         |
|                                                             |
|   login-app-default          spring-react-book-network      |
|   +----------------+        +-------------------------+    |
|   |    nginx       |   XX   | book-management-backend |    |
|   +----------------+        +-------------------------+    |
|                                                             |
|   XX = Cannot communicate (different bridge networks)       |
|_____________________________________________________________|
```

> [!WARNING]
> Different bridge networks are isolated by default. Containers on one bridge network cannot reach containers on another bridge network without explicit configuration.

---

## ✅ The Solution: Multi-Network Attachment

To solve this, attach the `nginx` container to the second network so it can communicate with containers on both networks:

```bash
docker network connect spring_react_book_network nginx
# General syntax:
docker network connect <network_name> <container_name>
```

After this command, the `nginx` container belongs to **two networks simultaneously**:

1. `login-app-default`
2. `spring_react_book_network`

```text
 _____________________________________________________________
|              MULTI-NETWORK ATTACHMENT                       |
|                                                             |
|   login-app-default          spring-react-book-network      |
|   +----------------+        +-------------------------+    |
|   | frontend       |        | book-management-backend |    |
|   +----------------+        +-------------------------+    |
|          |                              |                   |
|          +------->  nginx  <------------+                   |
|                  (connected                                 |
|                   to both)                                  |
|_____________________________________________________________|
```

> [!TIP]
> The `nginx` container now acts as a **bridge between two isolated Docker networks**, functioning as a central reverse proxy. This is a common pattern for routing traffic across microservice boundaries.

---

## 🧪 Practical Example: Creating a Shared Network

This example demonstrates creating a custom bridge network and connecting two containers to it.

### Step 1: Create a Bridge Network

```bash
docker network create mynet
```

This creates a private bridge network. Containers inside this network can communicate with each other.

### Step 2: Launch Containers on the Network

```bash
# Create an Ubuntu container on mynet
docker run -it --network mynet ubuntu bash

# Create an Nginx container on mynet
docker run -d --network mynet nginx
```

Each container receives:
* A private IP address
* A network interface connected to `mynet`
* Access to Docker's internal DNS server

### Step 3: Verify Communication

From inside the Ubuntu container, ping the Nginx container by name:

```bash
ping mynginx    # Works — Docker DNS resolves the name automatically
```

```text
 _____________________________________________
|          BRIDGE NETWORK COMMUNICATION       |
|                                             |
|   ubuntu container \                        |
|                     ----> mynet             |
|   nginx container  /       |               |
|                            v               |
|                   Acts like a virtual       |
|                   LAN switch — all          |
|                   containers can            |
|                   communicate               |
|_____________________________________________|
```

> [!NOTE]
> By connecting both containers to the same network, you have established communication between two previously isolated containers using Docker's bridge networking and internal DNS resolution.

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Networking Fundamentals](./06-Networking-Fundamentals.md) | [README](../README.md) | [Real-World Examples](./08-Real-World-Examples.md) |

</div>
