## (1) Container Host

```text
 ___________________________________________
|                 HP-server                 |
|               ( )   ( )   ( )             |
|  =======================================  |
|               container-runtime           |
|  =======================================  |
|                 RHEL-OS                   |
|___________________________________________|

```

* In a server, if a container is running, then it is called a **container host** (naming convention).
* If no container is running and there is no container runtime, then it is called a **normal server**.


## (2) Container Client

```text
 ___________________________________________
|                                           |
|       ( )   ( )   ( )   (C)   ( )   ( )   |
|  ========================^==============  |
|               container-runtime           |
|  =======================================  |
|                 RHEL-OS                   |
|___________________________________________|
                           /
                          / 
           .-------------'
          /
 ________v________
|       VM        |
|-----------------|
| container tool  |       ____________
|                 |      |            |
| username: balu  |<-----|   Monitor  |
| password: 123   |      |____________|
|_________________|            ||
                            ========

```
(1) Normally, users take server access, and then they can access containers from that server.

(2) But in a production environment, we do not give direct access to the container host. Because if anyone mistakenly deletes any root disk, the entire server will go down, and all the applications running there will stop.

(3) For that, we create a separate server (or VM). Then, we install container tools inside that VM.

* **(i)** For every user, we will give a username and password.
* **(ii)** If any user wants to access a container—for example, container (C):
* **(iii)** To run that C-container, there will be certain commands.
* *Example:* `connect_c-container`



Automatically, the VM directly connects to the container host, and then we get access to that specific container.

We can only use container-related commands, and users are only given access to certain dedicated containers. We do not give access to all the containers present on that container host. All others are restricted.

> **Note:** But we are not using this now.

**Bastion Node:** It is like a mediator VM.

* By using this mediator VM (or server), we can change the configurations.
* This mediator VM or server is called a **bastion node**.

---

### (3) Container Images

```text
.----------------------------------------------------.
|                   CONTAINER IMAGE                  |
|  __________________________    __________________  |
| |       Lightweight        |  |   Application    | |
| |           OS             |  |    Libraries     | |
| |        (50 MB)           |  |     & Code       | |
| |__________________________|  |__________________| |
 '----------------------------------------------------'

```

(1) It consists of two things: a lightweight OS and application libraries.
(2) The application can be anything, like a website or an app.
(3) We can deploy it to the internet as well.


### (4) Container Registries

(1) In a company, we create multiple Docker images. To store them, we use a registry.

(2) There are two types of registries:

* Public
* Private

```text
___________________________________________________________
|                    CONTAINER REGISTRY                     |
|         (Stores all your Docker Images & Versions)        |
|                                                           |
|    [ App Image v1 ]    [ App Image v2 ]    [ App Image v3 ]|
|___________________________________________________________|
          ^                                       |
          | (Push: Upload image)                  | (Pull: Download image)
          |                                       v
   [ Developer Laptop ]                 [ Production Server ]

```

(3) As our application upgrades, we update our Docker image. We also save our old versions in that registry.

(4) We can download it anywhere just by using a command, even from another server.

### (5) Container Namespace

```text
                        ( Hotstar App )
                               |
                               v
 ___________________________________________________________
|                    ISOLATED NAMESPACE                     |
|                                                           |
|   [ Process ]         [ Task Manager ]       [ Storage ]  |
|                                                           |
|   [ File System ]     [ Interface ]          [ ID ]       |
|___________________________________________________________|
        (Dedicated completely to this container only!)

```

* For every container, it will create a separate:
* Process
* Task manager
* Storage
* File system
* Interface
* ID


* These are dedicated to this container only.
* If we launch another container, it will create the exact same setup for that container as well.

### (6) Container

* It is nothing but a process, just like an application running on our laptop (e.g., Chrome, WhatsApp, etc.).
* If we open Task Manager and click "End Task" on it, the running container will also stop.

___________________________________________________________
|                       LAPTOP / HOST                       |
|  ________________________       ________________________  |
| |      Task Manager      |     |   Running Container    | |
| |  [x] Chrome            |     |                        | |
| |  [x] WhatsApp          |     |  (Runs just like a     | |
| |  [x] Container Process |<----+   normal laptop app)   | |
| |________________________|     |________________________| |
|              |                                            |
|              '-----> (Click "End Task" -> Container Stops) |
|___________________________________________________________|
---

### (7) Container Runtime Plugin

[ Kubernetes Node / Kubelet ]
                     |
                     | (Talks via CRI - Container Runtime Interface)
                     v
 ___________________________________________________________
|               HIGH-LEVEL CONTAINER RUNTIME                |
|         (Downloads Images, Manages Networks & Configs)    |
|                                                           |
|       .-------------------.       .-------------------.   |
|      |    containerd     |  -or-  |       cri-o       |   |
|       '-------------------'       '-------------------'   |
|_________________|___________________________|_____________|
                  |                           |
                  '-------------+-------------'
                                | (Passes bundle specification)
                                v
 ___________________________________________________________
|                LOW-LEVEL CONTAINER RUNTIME                |
|          (Talks directly to Linux Kernel to start App)    |
|                                                           |
|                        [ runc ]                           |
|___________________________|_______________________________|
                            |
                            v
             ( Creates Isolated Namespace )
             .----------------------------.
            |    [ Running Container ]    |
             '----------------------------'


* There are three runtime plugins:
* **(i) dockerd** $\rightarrow$ We are not using this at present.
* **(ii) cri-o** * **(iii) containerd**
* *(Note: We can use both of these.)*



### (8) Monolithic and Microservices

```text
_______________________________________________
|               SINGLE CONTAINER                |
|   ___________     ___________     ___________ |
|  |   Train   |   |   Movie   |   |  Flight   ||
|  |  Booking  |   |  Booking  |   |  Booking  ||
|  |___________|   |___________|   |___________||
|       ^                                       |
|_______|_______________________________________|
        |
  [ Updating... ] ---> (Entire container stops! Website goes down!)

```

(1) If there is a new update for train booking, we have to take down the website to run the new update.

(2) But since we are running all three bookings inside a single container, all 3 bookings will stop if we try to update even one of them.

(3) If there is any update for flight booking, we again have to stop the entire server.

(4) While I am updating one feature, if users want to access the other two, they will also be down. So, they will go to another source like BookMyShow, and our business will face a loss. This is called **monolithic**.

---

### But in Microservices

```text
_______________________________________________
|              SEPARATE CONTAINERS              |
|   ___________     ___________     ___________ |
|  | Container |   | Container |   | Container ||
|  |  _______  |   |  _______  |   |  _______  ||
|  | | Train | |   | | Movie | |   | |Flight | ||
|  | |Booking| |   | |Booking| |   | |Booking| ||
|  | |_______| |   | |_______| |   | |_______| ||
|  '_____._____'   '_____._____'   '_____._____'|
|________|_______________|_______________|______|
         |               |               |
   [ Updating ]      [ Works ]       [ Works ]
         |
         v
   (Only Train is down)

```

* We will host everything separately. So, only the one being updated will be down.
* The remaining will work perfectly.


* Dedicated features are running in dedicated containers, so there is less downtime and it will not affect other features.
* After Kubernetes came, it is 0-downtime.

### (6) Nginx Deployment Component

* Nginx is nothing but a high-performance web server, load balancer, and reverse proxy engine.
* In a microservices setup, we run Nginx inside its own separate, independent container.
* It intercepts incoming web requests from customers, serves lightweight static assets quickly, and routes the API traffic to the correct backend application containers.

```text
[ User Browser / Client Request ]
                       |
                       | (Public Traffic: Port 80 / 443)
                       v
 ___________________________________________________________
|                ISOLATED DOCKER NETWORK                    |
|                                                           |
|               .-----------------------.                   |
|              |    Nginx Container    |                   |
|               '-----------+-----------'                   |
|                           |                               |
|             .-------------+-------------.                 |
|             |                           |                 |
|             | (If path is "/")          | (If path is "/api")
|             v                           v                 |
|     .---------------.           .---------------.         |
|    |   Frontend    |           |    Backend    |         |
|    |   Container   |           |   Container   |         |
|     '---------------'           '---------------'         |
|___________________________________________________________|
```

---

### (7) Environment Setup: VM, Ubuntu, and Runtime Installation

* To set up our container playground, first, we create a clean Virtual Machine (VM).
* Inside that VM, we install the Ubuntu operating system (OS).
* Once the Ubuntu OS is up and running, we open the terminal and install the container runtime engine step-by-step using these sequential commands:

**Step 1: Update the local package tracking repository list**

```bash
sudo apt update

```

**Step 2: Download and install the core Docker engine utilities**

```bash
sudo apt install docker.io -y

```

**Step 3: Enable and instantly start the Docker background service**

```bash
sudo systemctl enable --now docker

```