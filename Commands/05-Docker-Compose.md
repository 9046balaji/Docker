<div align="center">
  <h1>🐙 Docker Compose Commands</h1>
  <p><strong>Manage multi-container applications with Docker Compose — from basic operations to scaling and multi-stage builds</strong></p>
  <img src="https://img.shields.io/badge/Level-Intermediate-yellow?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-8%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Section-05%20of%2011-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [What Is Docker Compose?](#-what-is-docker-compose)
2. [Core Commands](#-core-commands)
3. [Scaling Services](#-scaling-services)
4. [Networking in Compose](#-networking-in-compose)
5. [CI/CD and Image Management](#-cicd-and-image-management)
6. [Multi-Stage Builds](#-multi-stage-builds)
7. [Additional Useful Commands](#-additional-useful-commands)

---

## 🤔 What Is Docker Compose?

Docker Compose is a tool for defining and managing multi-container applications. Instead of running each container manually, you describe all your services in a single `docker-compose.yml` file and control them together.

```text
 _____________________________________________
|          DOCKER COMPOSE APPLICATION         |
|                                             |
|   +----------+        +----------+          |
|   | Frontend |        | Database |          |
|   | (React)  |        | (MySQL)  |          |
|   +----------+        +----------+          |
|                                             |
|   +----------+        +----------+          |
|   | Backend  |        |  Redis   |          |
|   | (Node)   |        | (Cache)  |          |
|   +----------+        +----------+          |
|                                             |
|   All managed using one docker-compose.yml  |
|_____________________________________________|
```

---

## ⚙️ Core Commands

| Command | Description |
|---------|-------------|
| `docker compose up` | Starts all services defined in the Compose file |
| `docker compose up -d` | Starts all services in detached mode (background) |
| `docker compose down` | Stops and removes all containers, networks, and volumes created by Compose |
| `docker compose stop` | Stops all running containers without removing them |
| `docker compose logs` | Displays logs from all services |
| `docker compose logs -f app` | Follows live logs for a specific service |
| `docker compose build` | Rebuilds all images without starting containers |
| `docker compose up --build` | Rebuilds images and then starts all containers |
| `docker compose exec app bash` | Opens an interactive shell inside a running service |

---

## 📈 Scaling Services

```bash
docker compose up --scale web=3
```

This command creates 3 separate container instances (replicas) of the `web` service.

```text
 _________________________________________
|           SCALED WEB SERVICE            |
|                                         |
|   web-1 ---|                            |
|            |                            |
|   web-2 ---|--- compose-default-network |
|            |                            |
|   web-3 ---|                            |
|_________________________________________|
```

```text
 _________________________________________________
|           LOAD DISTRIBUTION WITH SCALING         |
|                                                  |
|                  [ User Traffic ]                 |
|                       |                          |
|                       v                          |
|              [ Load Balancer ]                   |
|              /       |       \                   |
|             v        v        v                  |
|         [ web-1 ] [ web-2 ] [ web-3 ]            |
|            |         |         |                 |
|            +----+----+----+----+                 |
|                 |              |                 |
|            [ database ]  [ redis ]               |
|__________________________________________________|
```

### When to Scale

* Many users are accessing the application simultaneously
* A single container cannot handle the traffic load
* You want to improve performance through load distribution

### Characteristics of Scaled Containers

Each replica is:
* **Separate** — Runs as an independent container instance
* **Identical** — Created from the same image with the same configuration
* **Independent** — Has its own process space, memory, and filesystem

### What Scaling Is Good For

* Backend API services
* Frontend web servers
* Node.js applications
* Nginx reverse proxy instances
* Microservice architectures

### What Scaling Is NOT Good For

> [!WARNING]
> Scaling a database service directly is problematic because:
> * Data synchronization between replicas becomes complex
> * Storage consistency issues can lead to data corruption
> * Databases typically require specialized clustering solutions (e.g., MySQL replication, PostgreSQL streaming replication)

---

## 🌐 Networking in Compose

All replicas automatically join the same Compose network, enabling internal container-to-container communication.

> [!NOTE]
> **Compose scaling is primarily intended for:**
> * Local development and testing
> * Learning and experimentation
>
> **For production-grade scaling, use:**
> * **Docker Swarm** — Docker's native clustering solution
> * **Kubernetes** — Industry-standard container orchestration

---

## 🚀 CI/CD and Image Management

```bash
docker compose up --pull always
```

This command always downloads the latest image versions from the registry before starting containers. This is particularly useful in CI/CD pipelines to ensure you are always deploying the most recent build.

---

## 🏗️ Multi-Stage Builds

Multi-stage builds allow you to create different image variants from a single Dockerfile.

* **Build a development image:**
  ```bash
  docker build --target development -t myapp:dev .
  ```
  > [!NOTE]
  > The `.` tells Docker to look for the Dockerfile in the current working directory.

* **Build a production image:**
  ```bash
  docker build --target production -t myapp:prod .
  ```
  The production image is typically smaller, faster, and more secure because it excludes development dependencies and tools.

---

## 🧰 Additional Useful Commands

| Command | Description |
|---------|-------------|
| `docker compose ps` | Lists all containers managed by the current Compose project |
| `docker compose config` | Validates and displays the resolved Compose configuration |
| `docker compose pull` | Pulls the latest images for all services without starting containers |

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Development Workflows](./04-Development-Workflows.md) | [README](../README.md) | [Networking Fundamentals](./06-Networking-Fundamentals.md) |

</div>