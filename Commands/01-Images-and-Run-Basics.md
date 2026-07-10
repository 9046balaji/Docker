<div align="center">
  <h1>🐳 Docker Images and Run Basics</h1>
  <p><strong>Essential commands for pulling images, running containers, and managing tags</strong></p>
  <img src="https://img.shields.io/badge/Level-Beginner-green?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-8%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Section-01%20of%2011-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [Docker Environment Info](#-docker-environment-info)
2. [Pulling Images](#-pulling-images)
3. [Listing and Inspecting Images](#-listing-and-inspecting-images)
4. [Running Containers](#-running-containers)
5. [Image Tagging](#-image-tagging)
6. [Port Mapping and Options](#-port-mapping-and-options)

---

## 🔍 Docker Environment Info

* **`docker version`** → Verifies that Docker is installed and displays both the client and engine versions.
* **`docker info`** → Provides a detailed summary of the local Docker setup, including plugins, storage drivers, and engine details.

---

## 📥 Pulling Images

```text
 _______________________________________________
|              IMAGE PULL WORKFLOW              |
|                                               |
|   [ Registry ]  --->  [ Local Storage ]       |
|   (Docker Hub)        (docker images)         |
|                                               |
|   docker pull ubuntu:latest                   |
|       |                                       |
|       +--> Downloads all layers               |
|       +--> Stores image locally               |
|_______________________________________________|
```

* **`docker pull ubuntu:latest`** or **`docker image pull nginx`** → Downloads an image from a registry. If no tag is specified, Docker defaults to `latest`. If no registry is specified, Docker uses Docker Hub.
  * *Example:* `docker pull ubuntu` or `docker pull alpine` → Pulls the latest images from Docker Hub.

> [!WARNING]
> **Avoid using `latest` in production.** Today, version 24.5 might be tagged as `latest`. But when you deploy later, `latest` may point to version 25.2, potentially causing unexpected breaking changes. Always use a specific version tag for production environments (e.g., `nginx:1.25`).

---

## 📋 Listing and Inspecting Images

* **`docker images`** or **`docker image ls`** → Lists all images stored locally on your machine.
* **`docker images -q`** → Prints only the image IDs instead of the full table.
* **`docker images --digests`** → Displays the digest (a unique SHA256 hash) of each image. This hash uniquely identifies the exact image content, ensuring you are running precisely the version you expect.

> [!NOTE]
> The SHA256 digest is unique for every image version. Two images with the same tag but different digests contain different content.

* **`docker image inspect <name>`** or **`docker inspect <image>`** → Displays detailed metadata about an image, including its layers, configuration, environment variables, and architecture.

---

## ▶️ Running Containers

* **`docker run`** → Creates a new container from an image. If the image is not available locally, Docker pulls it automatically before starting the container.
* **`docker container ls`** or **`docker ps`** → Shows only the currently running containers.
* **`docker ps -a`** → Shows all containers, including stopped ones.

* **`docker start nginx`** → Starts an existing, previously created container named `nginx`. This command only works with containers that have already been created using `docker run`.

> [!IMPORTANT]
> Running `docker run -d nginx` twice creates **two separate containers** from the same `nginx` image. Each container is an independent instance with its own filesystem, network, and process space.

### Common `docker run` Flags

| Flag | Description |
|------|-------------|
| `-d` | Run in detached mode (container runs in the background) |
| `--name "name"` | Assign a custom name to the container |
| `-p 8080:80` | Map a host port to a container port (host:container) |
| `-e VAR=val` | Set an environment variable inside the container |
| `-v /host:/container` | Mount a host directory as a volume in the container |
| `-it` | Open an interactive terminal session |

* **`docker run -it ubuntu bash`** → Launches an Ubuntu container with an interactive bash shell. This gives you a terminal environment that behaves like a standalone Ubuntu system.

---

## 🏷️ Image Tagging

```text
 _______________________________________________
|              IMAGE TAGGING CONCEPT            |
|                                               |
|   nginx:latest ------+                        |
|                      |                        |
|                      +--->  IMAGE ID: a1b2c3  |
|                      |      (same data)       |
|   myregistry/  ------+                        |
|   nginx:v1.0                                  |
|                                               |
|   Two tags, one image. No data is duplicated. |
|_______________________________________________|
```

* **`docker image tag nginx myregistry/nginx:v1.0`** → Creates an additional reference (tag) pointing to the same image ID. Both the original and the new tag reference identical image data.

**Common use cases for tagging:**
* Versioning images (e.g., `v1.0`, `v2.0`)
* Preparing images for push to a registry
* Differentiating images in CI/CD pipelines
* Separating images by environment (e.g., `dev`, `staging`, `prod`)

* **`docker rmi myregistry/nginx:v1`** → Removes only the specified tag reference.

> [!NOTE]
> The actual image data is deleted only when **no tags** reference it and **no containers** are using it.

---

## 🔌 Port Mapping and Options

```text
 _______________________________________________
|              PORT MAPPING FLOW                |
|                                               |
|   Browser                                     |
|     |                                         |
|     v                                         |
|   localhost:8080  (Host Port)                 |
|     |                                         |
|     v  [Docker Network Layer]                 |
|     |                                         |
|     v                                         |
|   container:80    (Container Port)            |
|     |                                         |
|     v                                         |
|   [ nginx serving web page ]                  |
|_______________________________________________|
```

```bash
docker run -d --name nginx -p 8080:80 nginx
```

| Option | Purpose |
|--------|---------|
| `-d` | Run in detached mode (background) |
| `--name` | Assign a name to the container |
| `-p 8080:80` | Map host port 8080 to container port 80 |
| `-e VAR=val` | Set environment variables |
| `-v /host:/container` | Mount volumes |
| `-it` | Interactive terminal |

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Installation](../Installation.md) | [README](../README.md) | [Container Lifecycle](./02-Container-Lifecycle.md) |

</div>
