<div align="center">
  <h1>🔍 Debugging and Inspection Commands</h1>
  <p><strong>Tools for troubleshooting, inspecting, and diagnosing Docker containers and networks</strong></p>
  <img src="https://img.shields.io/badge/Level-Intermediate-yellow?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-8%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Section-03%20of%2011-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [Debugging Workflow](#-debugging-workflow)
2. [Container Logs](#-container-logs)
3. [Container Inspection](#-container-inspection)
4. [Resource Monitoring](#-resource-monitoring)
5. [Interactive Shell Access](#-interactive-shell-access)
6. [Docker Debug Tool](#-docker-debug-tool)
7. [Port and Network Debugging](#-port-and-network-debugging)
8. [Real-Time Event Monitoring](#-real-time-event-monitoring)

---

## 🗺️ Debugging Workflow

```text
 ___________________________________________________________________
|                     DEBUGGING DECISION TREE                       |
|                                                                   |
|   Container not working?                                          |
|        |                                                          |
|        v                                                          |
|   [ docker logs ]  --->  Check application output and errors      |
|        |                                                          |
|        v                                                          |
|   [ docker inspect ]  --->  Check config, state, and network      |
|        |                                                          |
|        v                                                          |
|   [ docker stats ]  --->  Check CPU, memory, and disk usage       |
|        |                                                          |
|        v                                                          |
|   [ docker exec ]  --->  Get shell access and run commands        |
|        |                                                          |
|        v                                                          |
|   [ docker debug ]  --->  Advanced debugging (distroless images)  |
|___________________________________________________________________|
```

---

## 📋 Container Logs

* **`docker logs app`** → Displays application output, errors, and startup messages.
* **`docker logs -f app`** → Follows logs in real time (streams new output as it appears).
* **`docker logs --tail 50 app`** → Displays only the last 50 lines of logs.
* **`docker logs --since 10m app`** → Displays logs from the last 10 minutes.
  * Other supported intervals: `--since 1h` (last hour), `--since 30s` (last 30 seconds).

---

## 🔎 Container Inspection

* **`docker inspect app`** → Returns a comprehensive JSON output containing:
  * Mounts and volume bindings
  * Container state (running, stopped, exit code)
  * Network settings and IP address
  * Port mappings
  * Environment variables
  * Restart policy

### Targeted Inspection with Format Flags

Instead of parsing the full JSON output, you can extract specific values directly:

* **`docker inspect -f '{{.State.Status}}' app`** → Returns only the container status (e.g., `running`, `exited`).
* **`docker inspect -f '{{.NetworkSettings.IPAddress}}' app`** → Returns the container's IP address.
* **`docker inspect -f '{{.RestartCount}}' app`** → Returns the number of times the container has restarted.
* **`docker inspect -f '{{.Config.Image}}' app`** → Returns the image name used to create the container.

---

## 📊 Resource Monitoring

* **`docker stats`** → Displays live resource usage for all running containers (CPU, memory, network I/O, disk I/O).
* **`docker stats app`** → Monitors the resource usage of a specific container.

---

## 💻 Interactive Shell Access

* **`docker exec -it app sh`** → Opens an interactive shell inside a running container.

> [!TIP]
> Once inside the container, you can run commands like `ls`, `pwd`, `env`, `cat`, and others to inspect the filesystem, check environment variables, and diagnose issues.

---

## 🐛 Docker Debug Tool

* **`docker debug app`** → Launches an advanced debugging session inside a running container.
* **`docker debug nginx:latest`** → Debugs an image without requiring a running container.
* **`docker debug app --shell bash`** → Starts a debugging shell using bash instead of the default shell.

### Why Docker Debug Is Better Than `docker exec`

Modern containers are often minimal, optimized, and security-hardened:

* **Distroless images** may contain only the application binary with no shell at all, causing `docker exec` to fail entirely.
* **Minimal images** often lack common debugging tools such as:
  * `bash`, `curl`, `ping`, `ps`, `netstat`

**Docker Debug solves these problems** by providing a fully equipped debugging environment that can:
* Inspect the filesystem of any container or image
* Verify installed packages and dependencies
* Examine configuration files
* Troubleshoot image build issues

> [!IMPORTANT]
> Docker Debug works even with distroless and scratch-based images where `docker exec` cannot function because no shell exists inside the container.

---

## 🌐 Port and Network Debugging

### Port Mapping Inspection

* **`docker port app`** → Displays the port mapping for a container.
  * Example output: `80/tcp -> 0.0.0.0:8080`
  * This means the application runs on port 80 inside the container, and you can access it from a browser at `http://localhost:8080`.

### Network Inspection

* **`docker network inspect bridge`** → Displays detailed information about Docker's default bridge network, including:
  * Connected containers
  * IP addresses assigned to each container
  * Gateway address
  * Network configuration settings

### Connectivity Testing

* **`docker exec app ping 8.8.8.8`** → Tests external internet connectivity from inside the container (8.8.8.8 is Google's public DNS).
* **`docker exec app curl http://localhost:8080`** → Tests whether the web server is accessible from inside the container itself.

---

## 📡 Real-Time Event Monitoring

* **`docker events`** → Streams real-time events from the Docker daemon, including container starts, stops, creation, deletion, and network changes. This is useful for monitoring the overall state of your Docker environment.
* **`docker events --filter type=container`** → Filters events to show only container-related events.

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Container Lifecycle](./02-Container-Lifecycle.md) | [README](../README.md) | [Development Workflows](./04-Development-Workflows.md) |

</div>