## Docker Networking

A Docker network is a virtual private network created by Docker for container communication.

It allows containers to:

* Talk to each other
* Isolate traffic
* Expose services
* Use DNS-based discovery

---

### Without Network

Containers are isolated; they cannot talk to each other.

---

### `docker network create mynet`

Docker creates:

* A virtual bridge switch
* A subnet
* A gateway
* A DNS system
* Internal routing rules

---

### Bridge Driver Use

Docker creates a virtual Ethernet bridge—like a virtual LAN switch.

#### Containers attached to the same bridge:

* Can communicate
* Get private IPs
* Resolve names


## Docker Internal DNS

Containers can use container names directly to communicate.

**Ex:**

* `docker run --name db --network mynet postgres`
* `docker run --name app --network mynet ubuntu`

**Inside app container:**

* `ping db` $\rightarrow$ works automatically

Docker provides internal DNS resolution.

```text
frontend network → backend API → database network

```

---

* Containers on different bridge networks cannot directly communicate:
* Unless explicitly connected
* This provides network isolation



### Bridge Network (By default complete isolation)

`docker run --network none ubuntu` $\rightarrow$ container gets:

* No internet
* No networking
* **Complete isolation**

---

`docker run --network host nginx` $\rightarrow$ container shares host network stack:

* No isolation
* Containers directly use:
* Host ports
* Host interface


## Container Port Mapping vs Network

### Internal Container Communication

* Uses:
* Docker network
* Container IP
* Container DNS name


* **Ex:** `backend` $\rightarrow$ `postgres`

---

### External Host Access

* Uses:
* `-p 8080:80`
* Host port 8080 $\rightarrow$ Container port 80



#### Architecture

Browser $\rightarrow$ `localhost:8080` $\rightarrow$ Docker port mapping $\rightarrow$ `nginx` container $\rightarrow$ `backend` container $\rightarrow$ `postgres` container

---

### IPAM

* IP Address Management
* Docker automatically manages:
* Subnet allocation
* IP assignment
* Gateways

### Connecting containers on different bridge networks:

* Containers on different bridge networks are isolated by default.
* To allow communication, you need to connect the container to the other network.

```bash
docker run -d \
  --name nginx \
  --network login-app-default \
  -p 80:80 \
  -v ${PWD}/nginx.conf:/etc/nginx/nginx.conf \
  nginx

```

* **`--network login-app-default`** $\rightarrow$ Connects the nginx container to the `login-app-default` bridge network. This means nginx can now communicate with containers inside the `login-app-default` bridge network.
* **`-p 80:80`** $\rightarrow$ Host:container. Browser traffic reaches the nginx container.
* **`-v ${PWD}/nginx.conf:/etc/nginx/nginx.conf`** $\rightarrow$ We created a file on our local machine named `nginx.conf`, and now we have copied/mounted that file into the container.
* **`nginx.conf`** $\rightarrow$ Custom nginx configuration.

