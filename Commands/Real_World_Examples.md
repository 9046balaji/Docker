* **To find the number of networks that exist on this system:**
* **`docker network ls`** $\rightarrow$ Lists the networks and their drivers (e.g., `bridge`, `host`, `none`).



---

* **If we inspect an image:**
* **`docker inspect image-name`**
* This shows **static details** of the blueprint/template. It contains:
1. Layers
2. Architecture
3. Environment variables
4. Default commands
5. Size





---

* **If we inspect a container:**
* **`docker inspect container-name`**
* This shows **dynamic details** about the running instance. It contains:
1. State
2. Network settings
3. Mounts
4. Host config
5. Log path


* **To attach a container to the `none` network and name it:**
```bash
docker run -d --name alpine-2 --network none alpine

```



---

* **To create a network named `my-sql` using the bridge driver, allocating the subnet as `182.18.0.0/24` and configuring the gateway as `182.18.0.1`:**
```bash
docker network create \
  --driver bridge \
  --subnet 182.18.0.0/24 \
  --gateway 182.18.0.1 \
  my-sql

```



---

* **To deploy a MySQL database using the `mysql:8.4` image, naming it `mysqldb`, attaching the newly created network `my-sql`, and setting the database password to `db-pass123`:**
```bash
docker run -d --name mysqldb \
  --network my-sql \
  -e MYSQL_ROOT_PASSWORD=db-pass123 \
  mysql:8.4

```



---

* **Create a web application connected to the `my-sql` network, map the host port `38080` to container port `8080`, and provide the database host and password to connect to the MySQL database:**
```bash
docker run -d \
  --name webapp \
  --network my-sql \
  -p 38080:8080 \
  -e DB_Host=mysqldb \
  -e DB_Password=db-pass123 \
  kodekloud/latest-webapp-mysql

```

* **Docker Hub** is a service that stores and distributes container images.
* **Docker Hub** is a hosted registry solution by Docker Inc. Besides public and private repositories, it also provides:
* Automated image builds
* Integration with source control platforms like:
* GitHub
* GitLab
* Bitbucket


* Webhooks
* Image versioning and tagging
* Team collaboration and access control
* Vulnerability scanning



---

* **Create a private Docker Registry named `my-registry`, map it to port 5000, and configure it to always restart automatically. This registry can be used to store and share Docker images locally without using Docker Hub:**
```bash
docker run -d \
  --name my-registry \
  -p 5000:5000 \
  --restart always \
  registry:2

```



---

* **Build a Docker image using a Dockerfile and name it `webapp-color` without specifying a tag:**
```bash
docker build -t webapp-color .

```


* *Note:* The `.` specifies to look for the Dockerfile in the current working directory.


* **To find the base operating system used by the `python:3.14` image:**
```bash
docker run -it python:3.14 bash
cat /etc/os-release

```


* *Note:* Run the first command to enter the bash shell for checking the OS, then run the second command.



---

* **Docker files related to Docker containers and images are stored in:**
`[ /var/lib/docker ]`

---

* **Run a database container and add data:**
```bash
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=db-pass123 \
  mysql

```


* If the database container crashes or is deleted, the data is also deleted.



---

* **To solve that, map a volume to the container so data is preserved even if the container is deleted, storing it in `/opt/data` on the host:**
```bash
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=db-pass123 \
  -v /opt/data:/var/lib/mysql \
  mysql

```

