## Containers

* **`docker container start nginx`** or **`docker start nginx`**
* **`docker container stop nginx`** or **`docker stop nginx`**
* **`docker container restart nginx`** or **`docker restart nginx`**
* **`docker container rm nginx`** or **`docker rm nginx`**
* **`docker container logs nginx`** or **`docker logs nginx`**
* **`docker logs -f nginx`** $\rightarrow$ Follow logs in real time.
* **`docker container inspect webserver`** or **`docker inspect webserver`**
* **`docker container exec -it webserver bash`**
* **`docker exec -it webserver ls /var/`**
* **`docker container top webserver`** $\rightarrow$ View running processes in a container.
* **`docker exec`** $\rightarrow$ Run commands inside a running container.
* **`docker container cp ./local/file.txt webserver:/app/`** $\rightarrow$ Copying local files into a container.
* **`docker cp webserver:/app/file.txt local.txt`** $\rightarrow$ Copying files from a container to local.
* **`docker container diff webserver`**