<div align="center">
  <h1>🧹 Docker System Maintenance</h1>
  <p><strong>Commands for monitoring disk usage, cleaning up unused resources, and maintaining a healthy Docker environment</strong></p>
  <img src="https://img.shields.io/badge/Level-Beginner-green?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-5%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Section-10%20of%2011-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [Disk Usage Monitoring](#-disk-usage-monitoring)
2. [System-Wide Cleanup](#-system-wide-cleanup)
3. [Targeted Cleanup Commands](#-targeted-cleanup-commands)
4. [System Information](#-system-information)

---

## 📊 Disk Usage Monitoring

* **`docker system df`** → Displays a summary of Docker's total disk usage, broken down by images, containers, volumes, and build cache.
* **`docker system df -v`** → Displays detailed (verbose) disk usage, showing which specific images, containers, and volumes are consuming space.

```text
 _______________________________________________
|          DOCKER DISK USAGE SUMMARY            |
|                                               |
|   TYPE            TOTAL    ACTIVE    SIZE     |
|   Images          15       5         3.2 GB   |
|   Containers      8        3         150 MB   |
|   Volumes         6        2         1.1 GB   |
|   Build Cache     —        —         500 MB   |
|                                               |
|   Total: 4.95 GB                              |
|_______________________________________________|
```

---

## 🗑️ System-Wide Cleanup

* **`docker system prune`** → Removes all of the following in one command:
  * Stopped containers
  * Unused networks
  * Dangling images (untagged intermediate layers)
  * Build cache

* **`docker system prune -a`** → Removes everything above **plus** all unused images (not just dangling ones).

> [!WARNING]
> **Understand the difference:**
> * **Dangling images** → Untagged, intermediate layers left over from builds
> * **Unused images** → Any image not currently referenced by a running or stopped container
>
> Using `-a` is more aggressive and will remove images you may want to use again later.

---

## 🎯 Targeted Cleanup Commands

For more precise cleanup, use these individual prune commands:

| Command | What It Removes |
|---------|----------------|
| `docker container prune` | All stopped containers |
| `docker image prune` | Dangling (untagged) images only |
| `docker image prune -a` | All unused images |
| `docker volume prune` | Unused volumes |
| `docker network prune` | Unused networks |
| `docker builder prune` | Build cache entries |

> [!CAUTION]
> **`docker volume prune`** permanently deletes volume data. Ensure you have backups of any important data stored in Docker volumes before running this command.

---

## ℹ️ System Information

* **`docker info`** → Displays detailed information about the Docker engine and system configuration, including:
  * Docker version and storage driver
  * Number of running, paused, and stopped containers
  * Number of images
  * Operating system and architecture
  * CPU and memory available to Docker
  * Installed plugins

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Registry Operations](./09-Registry-Operations.md) | [README](../README.md) | [Volumes & Mounts](./11-Volumes-and-Mounts.md) |

</div>