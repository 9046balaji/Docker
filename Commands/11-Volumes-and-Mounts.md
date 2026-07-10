## Volumes

* **`docker volume create mydata`** $\rightarrow$ Creates a managed storage area on the Docker host.
* **`docker volume ls`**
* **`docker volume inspect mydata`**
* **`docker volume rm mydata`** $\rightarrow$ Removes a volume.
* **`docker volume prune`** $\rightarrow$ Removes unused volumes.

**Structure created:**
`/var/lib/docker/volumes/mydata/_data/`

---

Containers normally have an isolated filesystem.

**Without mounts:**

* Container filesystem = private + temporary
* When a container is deleted:
* Files are deleted



**Mounts solve this:**

* Mount links: `HOST STORAGE <---> container path`
* So a container can:
* Read
* Write
* Persist data


* Outside its internal filesystem.

**Example:**

* `docker run -it -v mydata:/app ubuntu bash`
* `echo "hello" > temp.txt`
* `docker remove ID`
* `docker run -it -v mydata:/app ubuntu bash`
* `cat temp.txt`
* `(hello)`