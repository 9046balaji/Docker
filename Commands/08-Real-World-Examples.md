<div align="center">
  <h1>🌍 Real-World Docker Examples</h1>
  <p><strong>Practical, hands-on Docker scenarios covering networking, registries, Dockerfiles, and persistent storage</strong></p>
  <img src="https://img.shields.io/badge/Level-Intermediate-yellow?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-10%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Section-08%20of%2011-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [Listing Networks](#-listing-networks)
2. [Inspecting Images vs Containers](#-inspecting-images-vs-containers)
3. [Custom Network with Subnet and Gateway](#-custom-network-with-subnet-and-gateway)
4. [Deploying a MySQL Database](#-deploying-a-mysql-database)
5. [Connecting a Web Application to a Database](#-connecting-a-web-application-to-a-database)
6. [Docker Hub](#-docker-hub)
7. [Private Docker Registry](#-private-docker-registry)
8. [Building Images with Dockerfile](#-building-images-with-dockerfile)
9. [Finding the Base OS of an Image](#-finding-the-base-os-of-an-image)
10. [Docker Storage Location](#-docker-storage-location)
11. [Persistent Data with Volumes](#-persistent-data-with-volumes)

---

## 🔎 Listing Networks

* **`docker network ls`** → Lists all Docker networks on the system along with their drivers (e.g., `bridge`, `host`, `none`).

---

## 🔬 Inspecting Images vs Containers

### Image Inspection

```bash
docker inspect image-name
```

This shows **static details** of the image blueprint. It includes:
1. Layers that compose the image
2. Architecture (e.g., `amd64`, `arm64`)
3. Default environment variables
4. Default startup commands
5. Total image size

### Container Inspection

```bash
docker inspect container-name
```

This shows **dynamic details** about the running instance. It includes:
1. Current state (running, stopped, exit code)
2. Network settings and IP address
3. Volume mounts and bind mounts
4. Host configuration
5. Log file path

```text
 _____________________________________________
|       IMAGE vs CONTAINER INSPECTION         |
|                                             |
|   docker inspect <image>                    |
|   +-----------------+                       |
|   | Static Details  |  Layers, architecture |
|   | (Blueprint)     |  env vars, commands   |
|   +-----------------+                       |
|                                             |
|   docker inspect <container>                |
|   +-----------------+                       |
|   | Dynamic Details |  State, network, IPs  |
|   | (Instance)      |  mounts, logs, ports  |
|   +-----------------+                       |
|_____________________________________________|
```

---

## 🌐 Custom Network with Subnet and Gateway

To create a network named `my-sql` with a specific subnet and gateway:

```bash
docker network create \
  --driver bridge \
  --subnet 182.18.0.0/24 \
  --gateway 182.18.0.1 \
  my-sql
```

To attach a container to the `none` network (complete isolation):

```bash
docker run -d --name alpine-2 --network none alpine
```

---

## 🗄️ Deploying a MySQL Database

Deploy a MySQL 8.4 database container on the custom network:

```bash
docker run -d --name mysqldb \
  --network my-sql \
  -e MYSQL_ROOT_PASSWORD=db-pass123 \
  mysql:8.4
```

| Option | Purpose |
|--------|---------|
| `--name mysqldb` | Assigns a name for easy reference |
| `--network my-sql` | Connects to the custom bridge network |
| `-e MYSQL_ROOT_PASSWORD=db-pass123` | Sets the root password via an environment variable |

---

## 🔗 Connecting a Web Application to a Database

Deploy a web application on the same network as the database so they can communicate:

```bash
docker run -d \
  --name webapp \
  --network my-sql \
  -p 38080:8080 \
  -e DB_Host=mysqldb \
  -e DB_Password=db-pass123 \
  kodekloud/latest-webapp-mysql
```

```text
 ___________________________________________________
|           APPLICATION ARCHITECTURE                |
|                                                   |
|   Browser --> localhost:38080                      |
|                    |                               |
|                    v                               |
|              [ webapp:8080 ]                       |
|                    |                               |
|                    | (DB_Host=mysqldb)              |
|                    v                               |
|              [ mysqldb:3306 ]                      |
|                                                   |
|   Both containers on "my-sql" bridge network      |
|___________________________________________________|
```

> [!TIP]
> Because both containers are on the same `my-sql` network, the web application can reach the database using the container name `mysqldb` as the hostname — Docker DNS resolves it automatically.

---

## 📦 Docker Hub

**Docker Hub** is a hosted registry service by Docker Inc. that stores and distributes container images. It provides:

* **Public and private repositories** for storing images
* **Automated image builds** triggered by code changes
* **Integration with source control platforms:**
  * GitHub
  * GitLab
  * Bitbucket
* **Webhooks** for CI/CD pipeline triggers
* **Image versioning and tagging** for release management
* **Team collaboration and access control**
* **Vulnerability scanning** for security analysis

---

## 🏠 Private Docker Registry

Create a private Docker registry for storing and sharing images locally without using Docker Hub:

```bash
docker run -d \
  --name my-registry \
  -p 5000:5000 \
  --restart always \
  registry:2
```

| Option | Purpose |
|--------|---------|
| `-p 5000:5000` | Exposes the registry on port 5000 |
| `--restart always` | Ensures the registry restarts automatically if the container stops |
| `registry:2` | Uses version 2 of the official Docker registry image |

---

## 🏗️ Building Images with Dockerfile

Build a Docker image from a Dockerfile in the current directory:

```bash
docker build -t webapp-color .
```

> [!NOTE]
> The `.` at the end tells Docker to look for the `Dockerfile` in the current working directory. The `-t` flag assigns a name (tag) to the resulting image.

---

## 🔍 Finding the Base OS of an Image

To determine which operating system an image is based on:

```bash
# Step 1: Start an interactive shell inside the image
docker run -it python:3.14 bash

# Step 2: Inside the container, check the OS release information
cat /etc/os-release
```

---

## 📁 Docker Storage Location

All Docker-related files (images, containers, volumes, networks) are stored in:

```
/var/lib/docker
```

---

## 💾 Persistent Data with Volumes

### The Problem: Data Loss on Container Deletion

```bash
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=db-pass123 \
  mysql
```

> [!WARNING]
> If this database container crashes or is deleted, **all data stored inside it is permanently lost**. Container filesystems are ephemeral by default.

### The Solution: Mount a Volume

```bash
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=db-pass123 \
  -v /opt/data:/var/lib/mysql \
  mysql
```

```text
 _____________________________________________
|          VOLUME PERSISTENCE                 |
|                                             |
|   HOST                  CONTAINER           |
|   /opt/data  <------->  /var/lib/mysql      |
|                                             |
|   Container deleted?                        |
|   Data survives in /opt/data on the host.   |
|_____________________________________________|
```

The `-v /opt/data:/var/lib/mysql` flag maps the host directory `/opt/data` to the container's MySQL data directory. Even if the container is deleted, the data persists on the host and can be reattached to a new container.

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Cross-Network Communication](./07-Cross-Network-Communication.md) | [README](../README.md) | [Registry Operations](./09-Registry-Operations.md) |

</div>
