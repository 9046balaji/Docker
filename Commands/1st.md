## Commands

* **`docker version`** $\rightarrow$ Verifies that Docker is installed and that both the client and the server/engine are available.
* **`docker info`** $\rightarrow$ Gives a deeper summary of the local Docker setup, including plugins and engine details.
* **`docker images`** or **`docker image ls`** $\rightarrow$ Lists available images stored locally on our machine.
* **`docker pull ubuntu:latest`** or **`docker image pull nginx`** $\rightarrow$ Downloads an image from a registry. If we do not specify a tag, Docker assumes `latest`. If we do not specify a registry, Docker assumes and uses Docker Hub.
* *Example:* `docker pull ubuntu` or `docker pull alpine` $\rightarrow$ Pulls the latest images from Docker Hub.


* **`docker images -q`** $\rightarrow$ Prints only image IDs instead of the full table.
* **`docker images --digests`** $\rightarrow$ Shows the digest of images. A digest is a unique SHA256 hash that identifies the exact image content.

> **Note:** Today, version 24.5 might be the `latest` with a normal ID. When we pull it, it will download the latest version. However, later when we try that again in production, the `latest` version may point to 25.2, so it can cause trouble.

The SHA256 hash is different for everything.



* **`docker image inspect <name>`** or **`docker inspect <image>`** $\rightarrow$ Shows detailed metadata about an image.
* **`docker run`** $\rightarrow$ Creates a new container from an image (pulls the image if it is missing). It creates and starts the container.
* **`docker container ls`** or **`docker ps`** $\rightarrow$ Shows running containers only.
* **`docker ps -a`** $\rightarrow$ Shows all containers, including stopped ones.
* **`docker start nginx`** $\rightarrow$ Once a container is run and we assign the name `nginx` to it, we can run it using `docker start nginx`. It is used to run only existing containers.
* Running **`docker run -d nginx`** twice $\rightarrow$ Creates 2 separate containers, both created from the same `nginx` image.
* **`-d`** $\rightarrow$ Detached run (meaning the containers run in the background).
* **`--name "name"`** $\rightarrow$ Assigns a name while running so we can use that name to start or stop the container.
* **`docker image tag nginx myregistry/nginx:v1.0`** $\rightarrow$ Creates another reference/tag pointing to the same image ID (both point to the same image ID).
* **Why tags are used:**
* Mainly for versioning
* Pushing to registries
* CI/CD pipelines
* Environment separation

* **`docker rmi myregistry/nginx:v1`** $\rightarrow$ This removes only the tag reference.
* **Actual image is deleted only when:**
* No tags remain
* No containers use it


* **`docker run -d --name nginx -p 8080:80 nginx`**
* **`-d`**: Run in detached mode (background)
* **`--name`**: Assign container name
* **`-p 8080:80`**: Map port (host:container)
* **`-e VAR=val`**: Set environment variables
* **`-v /host:/container`**: Mount volumes
* **`-it`**: Interactive terminal


* **`docker run -it ubuntu bash`** $\rightarrow$ Run in an interactive shell
* Acts like an Ubuntu OS

