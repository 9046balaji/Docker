Docker Compose manages multiple containers together.

```text
| Frontend       Database |
| Backend        Redis    |

```

All managed using one `docker-compose.yml`.

* **`docker compose up`** $\rightarrow$ Starts all services.
* **`docker compose up -d`** $\rightarrow$ Keeps containers running in the background.
* **`docker compose logs`** $\rightarrow$ Shows logs from all services.
* **`docker compose logs -f app`** $\rightarrow$ Follows live logs.
* **`docker compose down`** $\rightarrow$ Stops and removes containers and networks.
* **`docker compose stop`** $\rightarrow$ Stops all containers of a Docker Compose application.
* **`docker compose build`** $\rightarrow$ Only rebuilds images.
* **`docker compose up --build`** $\rightarrow$ Rebuilds + starts containers.
* **`docker compose exec app bash`** $\rightarrow$ Opens a shell inside a running service.
* **`docker compose up --scale web=3`** $\rightarrow$ Runs 3 containers for the web service.
* $\downarrow$ Creates multiple replicas.



All replicas join the same compose network.

```text
web-1 ---|
         |
web-2 ---|--- compose-default
         |
web-3 ---|

```

## Scaling

Scaling is used when:

* Many users access the app
* One container is not enough
* We want better performance
* We want to distribute traffic

### These containers are:

* Separate
* Identical
* Independent

### Each has:

* Its own process
* Its own memory
* Its own filesystem

---

### Networking

All replicas join the same Docker network so containers can communicate internally.

---

### Scaling is mainly used for:

* Backend API
* Frontend services
* Node.js apps
* Nginx servers
* Microservices

### NOT Good For:

Scaling a database directly is harder because:

* Data synchronization is needed
* Storage consistency problems arise

---

> **Compose scaling is mainly for:**
> * Local development
> * Testing
> * Learning
> 
> 

> **For production:**
> * Docker Swarm
> * Kubernetes
> 
>

* **`docker compose up --pull always`** $\rightarrow$ Always downloads the latest image versions before starting.
* Useful in CI/CD pipelines.


* **`docker build --target development -t myapp:dev .`** $\rightarrow$ Builds only the development section.
* (The `.` points to the current folder).


* **`docker build --target production -t myapp:prod .`** $\rightarrow$ Creates an optimized production image.
* Faster, smaller, and secure.