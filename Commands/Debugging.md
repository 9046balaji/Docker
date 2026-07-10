## Debugging Commands

* **`docker logs app`** $\rightarrow$ Shows application output, errors, and startup messages.
* **`docker logs -f app`** $\rightarrow$ Shows logs in real time.
* **`docker logs --tail 50 app`** $\rightarrow$ Displays the last 50 lines.
* **`docker logs --since 10m app`** $\rightarrow$ Displays logs from the last 10 minutes.
* Can also use options like `--since 1h` or `--since 30s`.


* **`docker inspect app`** $\rightarrow$ Returns a huge JSON output that includes:
* Mounts
* Container state
* Network settings
* Ports
* Volumes
* IP address
* Environment variables
* Restart policy


* **`docker inspect -f '{{.State.Status}}' app`** $\rightarrow$ Instead of the full JSON, it outputs only the required value (container status).
* **`docker inspect -f '{{.NetworkSettings.IPAddress}}' app`** $\rightarrow$ Container IP.
* **`docker inspect -f '{{.RestartCount}}' app`** $\rightarrow$ Restart count.
* **`docker inspect -f '{{.Config.Image}}' app`** $\rightarrow$ Image name.
* **`docker stats`** $\rightarrow$ Shows live resource usage of running containers.
* **`docker stats app`** $\rightarrow$ To check CPU, RAM, memory, disk, and processes.
* **`docker exec -it app sh`** $\rightarrow$ Opens an interactive shell inside a running container.

> (We can run `ls`, `pwd`, etc., and check the files in it, including environment variables (env)).


* **`docker debug app`** $\rightarrow$ Debug a running container.
* **`docker debug nginx:latest`** $\rightarrow$ Debug an image without a running container.
* **`docker debug app --shell bash`** $\rightarrow$ Starts a debugging shell using bash instead of the default shell.

### It is better than `docker exec`

Modern containers are often tiny, optimized, and secure.

* *Example:* Distroless images
* They may contain only the application binary
* No shell at all
* So, `exec` may fail completely

And also, it does not contain most debugging tools:

* bash
* curl
* ping
* ps
* netstat

So, Docker debugger solves all these problems. It consists of an advanced debugging environment:

* Checking the filesystem
* Verifying packages
* Inspecting configs
* Debugging image build issues

Easier troubleshooting


## Port and Network Debugging

* **`docker port app`** → View port mapping.
* `80/tcp -> 0.0.0.0:8080`
* App runs inside the container on port 80.
* We can access it from a browser using `http://localhost:8080`.


* **`docker network inspect bridge`** → Shows detailed information about Docker's bridge network.
* **We can see:**
* Connected containers
* IP addresses
* Gateway
* Network settings




* **`docker exec app ping 8.8.8.8`** → Runs the ping command inside the container (8.8.8.8 is Google DNS).
* **`docker exec app curl http://localhost:8080`** → Tries to access the server from inside the same container.