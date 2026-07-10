<div align="center">
  <h1>💾 Docker Volumes and Mounts</h1>
  <p><strong>Understanding persistent storage in Docker — volumes, bind mounts, and tmpfs mounts</strong></p>
  <img src="https://img.shields.io/badge/Level-Intermediate-yellow?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-8%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Section-11%20of%2011-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [Why Volumes Are Needed](#-why-volumes-are-needed)
2. [Volume Management Commands](#-volume-management-commands)
3. [How Mounts Work](#-how-mounts-work)
4. [Practical Example: Data Persistence](#-practical-example-data-persistence)
5. [Types of Mounts](#-types-of-mounts)
6. [The --mount Syntax](#-the---mount-syntax)

---

## 🤔 Why Volumes Are Needed

Containers have an isolated, ephemeral filesystem by default. When a container is deleted, all data inside it is permanently lost.

```text
 _______________________________________________
|          WITHOUT VOLUMES                      |
|                                               |
|   +-------------------+                       |
|   |    Container      |                       |
|   |  +-----------+    |                       |
|   |  | data.db   |    |   docker rm -->  GONE |
|   |  | config    |    |   All data is lost!   |
|   |  +-----------+    |                       |
|   +-------------------+                       |
|                                               |
|          WITH VOLUMES                         |
|                                               |
|   HOST              CONTAINER                 |
|   /var/lib/docker    +-------------------+    |
|   /volumes/mydata    |  +-----------+    |    |
|   +-----------+      |  | /app/data |    |    |
|   | data.db   |<---->|  | data.db   |    |    |
|   | config    |      |  | config    |    |    |
|   +-----------+      |  +-----------+    |    |
|   (persists!)        +-------------------+    |
|                       docker rm --> Data safe! |
|_______________________________________________|
```

---

## 🔧 Volume Management Commands

| Command | Description |
|---------|-------------|
| `docker volume create mydata` | Creates a named volume managed by Docker |
| `docker volume ls` | Lists all volumes |
| `docker volume inspect mydata` | Displays detailed information about a volume |
| `docker volume rm mydata` | Removes a specific volume |
| `docker volume prune` | Removes all unused volumes |

**Volume storage location on the host:**
```
/var/lib/docker/volumes/mydata/_data/
```

---

## 🔗 How Mounts Work

Mounts create a link between host storage and a container path, allowing containers to read, write, and persist data outside their internal filesystem.

```text
 _______________________________________________
|              MOUNT MECHANISM                  |
|                                               |
|   HOST STORAGE  <========>  CONTAINER PATH    |
|                                               |
|   A container can:                            |
|     • Read files from the host                |
|     • Write files to the host                 |
|     • Persist data beyond its own lifetime    |
|_______________________________________________|
```

---

## 🧪 Practical Example: Data Persistence

This example demonstrates how volumes preserve data across container restarts and deletions.

**Step 1:** Create a container with a volume and write data:

```bash
docker run -it -v mydata:/app ubuntu bash
echo "hello" > /app/temp.txt
exit
```

**Step 2:** Remove the container:

```bash
docker rm <container_id>
```

**Step 3:** Create a new container with the same volume and verify the data:

```bash
docker run -it -v mydata:/app ubuntu bash
cat /app/temp.txt
# Output: hello
```

> [!TIP]
> The data persisted because it was stored in the `mydata` volume on the host, not inside the container's ephemeral filesystem. Even after the container was deleted, the volume retained the data.

---

## 📋 Types of Mounts

Docker supports three types of mounts, each suited for different use cases:

| Type | Description | Use Case |
|------|-------------|----------|
| **Named Volumes** | Managed by Docker, stored in `/var/lib/docker/volumes/` | Database storage, application data that must persist |
| **Bind Mounts** | Maps a specific host directory to a container path | Development workflows, sharing config files |
| **tmpfs Mounts** | Stored in host memory only (never written to disk) | Sensitive data (secrets, tokens) that should not persist |

```text
 _______________________________________________
|           THREE TYPES OF MOUNTS               |
|                                               |
|   Named Volume:                               |
|   docker run -v mydata:/app/data ...          |
|   (Docker manages the storage location)       |
|                                               |
|   Bind Mount:                                 |
|   docker run -v /home/user/src:/app/src ...   |
|   (You specify the exact host path)           |
|                                               |
|   tmpfs Mount:                                |
|   docker run --tmpfs /app/secrets ...         |
|   (Stored in memory only, never on disk)      |
|_______________________________________________|
```

---

## 🔧 The `--mount` Syntax

Docker also supports the `--mount` flag, which provides a more explicit and readable syntax compared to `-v`:

### Named Volume

```bash
docker run -d \
  --name db \
  --mount type=volume,source=mydata,target=/var/lib/mysql \
  mysql
```

### Bind Mount

```bash
docker run -d \
  --name app \
  --mount type=bind,source=$(pwd)/src,target=/app/src \
  myapp
```

### tmpfs Mount

```bash
docker run -d \
  --name secure-app \
  --mount type=tmpfs,target=/app/secrets \
  myapp
```

> [!NOTE]
> The `--mount` syntax is generally preferred over `-v` because it is more explicit and less error-prone. The `-v` flag silently creates directories if they don't exist, while `--mount` raises an error, helping catch configuration mistakes early.

---

<div align="center">

| ⬅️ Previous | 🏠 Home |
|:---:|:---:|
| [System Maintenance](./10-System-Maintenance.md) | [README](../README.md) |

</div>