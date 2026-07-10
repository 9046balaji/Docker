## Development Container Operations

* **`docker run -it --name dev-app -v $(pwd):/app myimage bash`**
* **`-it`** $\rightarrow$ Interactive terminal mode
* **`--name dev-app`** $\rightarrow$ Container app name
* **`-v $(pwd):/app`** $\rightarrow$ Mounts the current folder into the container
* **`myimage`** $\rightarrow$ Docker image
* **`bash`** $\rightarrow$ Opens a bash shell



### Why Use This?

* Testing code inside a container and debugging.
* Real-time development and editing files from the host machine.

---

### `-v $(pwd):/app`

**No rebuild required:** Any file changes inside the computer instantly appear inside the container.

**Project Structure Example:**

```text
project
 ├── app.py
 └── requirements.txt

```

* **`docker run -it -v $(pwd):/app python bash`**

**Inside the container:**

* `cd /app`
* `python app.py`

Your local code is now running inside Docker.

---

### Run App with Live Source Mount

```bash
docker run -d \
  --name app \
  -v $(pwd)/src:/app/src \
  -p 8080:8080 \
  myapp

```

* **`-v $(pwd)/src:/app/src`** $\rightarrow$ Mounts the entire source code.
* **`-p 8080:8080`** $\rightarrow$ Port mapping.

### In Real-Time Development

This setup is very common in:

* Node.js
* React
* Python Flask
* Spring Boot
* FastAPI

When we edit files locally, the container sees the changes immediately.

---

### Rebuild After Code Changes

```bash
docker compose up --build

```

**Flow:**
Code changes $\rightarrow$ Rebuild image $\rightarrow$ Restart containers

**Why needed:**

* Sometimes changes affect dependencies or the Docker environment setup.

---

```bash
docker compose restart app-service

```

**Use Case:**
Suppose:

* Only the backend changed
* The database is unchanged

Then restart only the backend. It is faster than restarting everything.


