### Problem Happened

We tried to connect `book-management-backend`, which is in another bridge network: `spring-react-book-network`.

* So, different bridge networks cannot communicate directly (they are isolated).

---

### Solved Problem

```bash
docker network connect spring_react_book_network nginx
docker network connect <network name> <container>

```

We attached the nginx container to another Docker network.

Now nginx belongs to:

1. `login-app-default`
2. `spring_react_book_network`

Now nginx can access both containers, which are in different bridge networks.

> Nginx container becomes a bridge between two isolated Docker networks (central server / reverse proxy).

### Connecting Containers to a Network

Created two containers:

* (i) ubuntu
* (ii) nginx

Both were connected to the `mynet` network.

```text
ubuntu container \
                  ----> mynet ----> communication works
nginx container  /      |
                        v
               Acts like a virtual LAN switch

```

---

1. **`docker network create mynet`** $\rightarrow$ Created a private bridge network; containers inside this same network can communicate.
2. **`docker run -it --network mynet ubuntu bash`**
* Created an Ubuntu container directly inside `mynet`.
* Container got:
* (i) private IP
* (ii) network interface
* (iii) access to Docker DNS




3. **`docker run -d --network mynet nginx`**
4. **Now if we ping mynginx from ubuntu, it will work.**
* We created communication between two containers which were isolated. We used a Docker network to create a bridge network and connected the two containers so they can communicate.

