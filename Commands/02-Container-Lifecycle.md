<div align="center">
  <h1>🔄 Container Lifecycle Commands</h1>
  <p><strong>Commands for starting, stopping, inspecting, and managing Docker containers</strong></p>
  <img src="https://img.shields.io/badge/Level-Beginner-green?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-5%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Section-02%20of%2011-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [Container State Diagram](#-container-state-diagram)
2. [Start, Stop, and Restart](#-start-stop-and-restart)
3. [Remove Containers](#-remove-containers)
4. [Logs and Inspection](#-logs-and-inspection)
5. [Execute Commands Inside Containers](#-execute-commands-inside-containers)
6. [Copy Files Between Host and Container](#-copy-files-between-host-and-container)
7. [Additional Lifecycle Commands](#-additional-lifecycle-commands)

---

## 📊 Container State Diagram

```text
                        CONTAINER LIFECYCLE
 
    docker run             docker stop             docker rm
  +----------+          +-------------+          +-----------+
  |          v          |             v          |           v
  |    +---------+      |      +----------+     |     +---------+
  |    | CREATED | ---->|      | STOPPED  | --->|     | REMOVED |
  |    +---------+      |      +----------+     |     +---------+
  |          |          |             ^          |
  |          v          |             |          |
  |    +---------+      |             |          |
  +--> | RUNNING | -----+             |          |
       +---------+                    |          |
            |       docker restart    |          |
            +-------------------------+          |
            |                                    |
            +------------------------------------+
```

> [!NOTE]
> A container transitions through these states during its lifecycle. Understanding these states helps you manage containers effectively.

---

## ▶️ Start, Stop, and Restart

* **`docker container start nginx`** or **`docker start nginx`** → Starts an existing stopped container.
* **`docker container stop nginx`** or **`docker stop nginx`** → Gracefully stops a running container by sending a SIGTERM signal.
* **`docker container restart nginx`** or **`docker restart nginx`** → Stops and then starts a container in one command.

---

## 🗑️ Remove Containers

* **`docker container rm nginx`** or **`docker rm nginx`** → Removes a stopped container. A running container must be stopped first, or use the `-f` flag to force removal.

---

## 📋 Logs and Inspection

* **`docker container logs nginx`** or **`docker logs nginx`** → Displays the standard output and error logs of a container.
* **`docker logs -f nginx`** → Follows the log output in real time (similar to `tail -f`).
* **`docker container inspect webserver`** or **`docker inspect webserver`** → Returns detailed JSON metadata about a container, including its network settings, mounts, state, and configuration.
* **`docker container top webserver`** → Displays the running processes inside a container.

---

## 💻 Execute Commands Inside Containers

* **`docker container exec -it webserver bash`** → Opens an interactive bash shell inside a running container.
* **`docker exec -it webserver ls /var/`** → Executes a single command (`ls /var/`) inside the container and returns the output.
* **`docker exec`** → Runs any command inside a running container without stopping it.

---

## 📁 Copy Files Between Host and Container

```text
 _______________________________________________
|          FILE COPY DIRECTIONS                 |
|                                               |
|   HOST  ---- docker cp local container --->   |
|              CONTAINER                        |
|                                               |
|   HOST  <--- docker cp container local ----   |
|              CONTAINER                        |
|_______________________________________________|
```

* **`docker container cp ./local/file.txt webserver:/app/`** → Copies a file from the host machine into the container at the specified path.
* **`docker cp webserver:/app/file.txt local.txt`** → Copies a file from inside the container to the host machine.

---

## 🔧 Additional Lifecycle Commands

* **`docker container diff webserver`** → Shows the changes made to the container's filesystem compared to its original image (added, modified, or deleted files).
* **`docker container rename old_name new_name`** → Renames an existing container.
* **`docker container pause webserver`** → Suspends all processes inside a container (freezes it without stopping).
* **`docker container unpause webserver`** → Resumes a paused container.
* **`docker container wait webserver`** → Blocks until a container stops, then prints its exit code.

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Images & Run Basics](./01-Images-and-Run-Basics.md) | [README](../README.md) | [Debugging & Inspection](./03-Debugging-and-Inspection.md) |

</div>