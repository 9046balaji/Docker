<div align="center">
  <h1>⚡ Development Container Workflows</h1>
  <p><strong>Commands and patterns for real-time development, live code mounting, and iterative container workflows</strong></p>
  <img src="https://img.shields.io/badge/Level-Intermediate-yellow?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-6%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Section-04%20of%2011-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [Interactive Development Container](#-interactive-development-container)
2. [Volume Mounting for Live Development](#-volume-mounting-for-live-development)
3. [Running Applications with Live Source Mounts](#-running-applications-with-live-source-mounts)
4. [Rebuilding After Code Changes](#-rebuilding-after-code-changes)
5. [Selective Service Restart](#-selective-service-restart)

---

## 🖥️ Interactive Development Container

```bash
docker run -it --name dev-app -v $(pwd):/app myimage bash
```

| Option | Description |
|--------|-------------|
| `-it` | Interactive terminal mode |
| `--name dev-app` | Assigns a name to the container |
| `-v $(pwd):/app` | Mounts the current host directory into the container at `/app` |
| `myimage` | The Docker image to use |
| `bash` | Opens a bash shell inside the container |

### Why Use This?

* **Testing:** Run and test code directly inside a container environment.
* **Debugging:** Inspect the container filesystem and diagnose issues interactively.
* **Real-time development:** Edit files on the host machine and see changes reflected instantly inside the container.

---

## 📂 Volume Mounting for Live Development

### How `-v $(pwd):/app` Works

```text
 _______________________________________________
|           VOLUME MOUNT (BIND MOUNT)           |
|                                               |
|   HOST MACHINE          CONTAINER             |
|   +-----------+         +-----------+         |
|   | project/  | <-----> | /app/     |         |
|   |  app.py   |  sync   |  app.py   |         |
|   |  req.txt  |         |  req.txt  |         |
|   +-----------+         +-----------+         |
|                                               |
|   Changes on either side are reflected        |
|   on the other side instantly.                |
|_______________________________________________|
```

**No rebuild required:** Any file changes on the host machine instantly appear inside the container.

**Project Structure Example:**

```text
project/
 ├── app.py
 └── requirements.txt
```

* **`docker run -it -v $(pwd):/app python bash`** → Mounts the current directory and opens a shell.

**Inside the container:**
* `cd /app`
* `python app.py`

Your local code is now running inside Docker without any rebuild step.

---

## 🚀 Running Applications with Live Source Mounts

```bash
docker run -d \
  --name app \
  -v $(pwd)/src:/app/src \
  -p 8080:8080 \
  myapp
```

| Option | Purpose |
|--------|---------|
| `-v $(pwd)/src:/app/src` | Mounts the entire source code directory |
| `-p 8080:8080` | Maps host port 8080 to container port 8080 |

### Common Frameworks That Use This Pattern

This live-mount development setup is widely used with:

* **Node.js** / **React** — File watchers detect changes automatically
* **Python Flask** / **FastAPI** — Debug mode reloads on file save
* **Spring Boot** — DevTools enables automatic restart on code changes

> [!TIP]
> When you edit files locally, the container sees the changes immediately. Combined with a framework's hot-reload feature, you get instant feedback without rebuilding the image.

---

## 🔨 Rebuilding After Code Changes

```bash
docker compose up --build
```

**Flow:** Code changes → Rebuild image → Restart containers

**When rebuilding is necessary:**

* Changes affect `Dockerfile` instructions (e.g., new dependencies added)
* Changes affect the Docker Compose configuration
* Changes require packages to be reinstalled inside the container

> [!NOTE]
> If only your application source code has changed and you are using volume mounts, a rebuild is usually unnecessary. Rebuilds are needed when the container environment itself must change.

---

## 🎯 Selective Service Restart

```bash
docker compose restart app-service
```

**Use case:** When only one service has changed and you want to avoid restarting the entire stack.

**Example scenario:**
* The backend service was updated with new code
* The database service is unchanged and should keep running

Restarting only the backend is faster and avoids unnecessary downtime for other services.

> [!TIP]
> Use `docker compose restart <service-name>` to restart individual services instead of bringing down the entire application with `docker compose down`.

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Debugging & Inspection](./03-Debugging-and-Inspection.md) | [README](../README.md) | [Docker Compose](./05-Docker-Compose.md) |

</div>
