<div align="center">
  <h1>📤 Docker Registry Operations</h1>
  <p><strong>Commands for logging in, tagging, pushing, pulling, and managing images in Docker registries</strong></p>
  <img src="https://img.shields.io/badge/Level-Beginner-green?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-5%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Section-09%20of%2011-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [Push/Pull Workflow](#-pushpull-workflow)
2. [Authentication](#-authentication)
3. [Tagging and Pushing Images](#-tagging-and-pushing-images)
4. [Pulling and Searching Images](#-pulling-and-searching-images)
5. [Removing Tagged Images](#-removing-tagged-images)
6. [Private Registries](#-private-registries)

---

## 🔄 Push/Pull Workflow

```text
 ___________________________________________________________
|                 REGISTRY PUSH/PULL FLOW                   |
|                                                           |
|   LOCAL MACHINE                     DOCKER HUB            |
|   +---------------+                +---------------+      |
|   | myapp:latest  |                |               |      |
|   +-------+-------+                |   username/   |      |
|           |                        |   myapp:v1.0  |      |
|     docker tag                     |               |      |
|           |                        +-------+-------+      |
|           v                                ^   |          |
|   +---------------+     docker push        |   |          |
|   | username/     | ---------------------->+   |          |
|   | myapp:v1.0    |                            |          |
|   +---------------+     docker pull            |          |
|   | (same image)  | <-------------------------+          |
|   +---------------+                                       |
|___________________________________________________________|
```

---

## 🔐 Authentication

* **`docker login`** → Authenticates with Docker Hub. You will be prompted for your username and password.
* **`docker logout`** → Logs out from Docker Hub.

> [!NOTE]
> You must be logged in before you can push images to your Docker Hub account. Pulling public images does not require authentication.

---

## 🏷️ Tagging and Pushing Images

Before pushing an image, you must tag it with your Docker Hub username and repository name:

* **`docker tag bike-showroom:latest balu98578/bike-showroom:latest`** → Creates a new tag `balu98578/bike-showroom:latest` that points to the same image as `bike-showroom:latest`.
* **`docker push balu98578/bike-showroom:latest`** → Uploads the tagged image to Docker Hub.

> [!IMPORTANT]
> **Without tagging, `docker push` will not work.** Docker requires the image name to include the registry path (e.g., `username/repository:tag`) before it can push to a remote registry.

---

## 📥 Pulling and Searching Images

* **`docker pull balu98578/bike-showroom:latest`** → Downloads the specified image from Docker Hub to your local machine.
* **`docker search nginx`** → Searches Docker Hub for images matching the keyword `nginx`. Returns a list of available images with descriptions and star ratings.

---

## 🗑️ Removing Tagged Images

* **`docker rmi balu98578/bike-showroom:latest`** → Removes the local tagged image only.

> [!NOTE]
> This command removes the tag reference from your local machine. The image remains available on Docker Hub, and the original untagged copy (if it exists) remains on your local machine as well.

---

## 🏢 Private Registries

In addition to Docker Hub, you can use private registries for storing images securely within your organization:

| Registry | Provider | Use Case |
|----------|----------|----------|
| **Docker Hub** | Docker Inc. | Public and private repositories |
| **Amazon ECR** | AWS | Private registry integrated with AWS services |
| **Google Container Registry (GCR)** | Google Cloud | Private registry integrated with GCP |
| **Azure Container Registry (ACR)** | Microsoft Azure | Private registry integrated with Azure |
| **Harbor** | CNCF (Open Source) | Self-hosted, enterprise-grade private registry |
| **GitHub Container Registry (GHCR)** | GitHub | Private registry integrated with GitHub |

To log in to a private registry:

```bash
docker login <registry-url>
# Example:
docker login mycompany.azurecr.io
```

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Real-World Examples](./08-Real-World-Examples.md) | [README](../README.md) | [System Maintenance](./10-System-Maintenance.md) |

</div>