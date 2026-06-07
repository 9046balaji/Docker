# Container Architecture and Isolation

This document covers the key components of container architecture and how they provide isolation for applications.

---

## Table of Contents

1. [Container Host](#1-container-host)
2. [Container Client](#2-container-client)  
3. [Container Images](#3-container-images)
4. [Container Registries](#4-container-registries)
5. [Container Namespace](#5-container-namespace)
6. [Container](#6-container)
7. [Container Runtime Plugin](#7-container-runtime-plugin)
8. [Monolithic and Microservices](#8-monolithic-and-microservices)
9. [Nginx Deployment Component](#9-nginx-deployment-component)
10. [Environment Setup](#10-environment-setup-vm-ubuntu-and-runtime-installation)
11. [Key Benefits Summary](#11-key-benefits-summary)

---

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

---

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

(2) But in a production environment, we do not give direct access to the container host. This is because if anyone mistakenly deletes any root disk, the entire server will go down, and all the applications running there will stop.

(3) For that, we create a separate server (or VM). Then, we install container tools inside that VM.

* **(i)** For every user, we will give a username and password.
* **(ii)** If any user wants to access a container—for example, container (C):
* **(iii)** To run that C-container, there will be certain commands.
* **Example:** `connect_c-container`

Automatically, the VM directly connects to the container host, and then we get access to that specific container.

We can only use container-related commands, and users are only given access to certain dedicated containers. We do not give access to all the containers present on that container host. All others are restricted.

> **Note:** But we are not using this now.

**Bastion Node:** It is like a mediator VM.

* By using this mediator VM (or server), we can change the configurations.
* This mediator VM or server is called a **bastion node**.

---

## (3) Container Images

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

---

## (4) Container Registries

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

---

## (5) Container Namespace

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

For every container, it will create a separate:
* Process
* Task manager
* Storage
* File system
* Interface
* ID

These are dedicated to this container only. If we launch another container, it will create the exact same setup for that container as well.

---

## (6) Container

* It is nothing but a process, just like an application running on our laptop (e.g., Chrome, WhatsApp, etc.).
* If we open Task Manager and click "End Task" on it, the running container will also stop.

```text
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
```

---

## (7) Container Runtime Plugin

```text
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
```

There are three runtime plugins:
* **(i) dockerd** → We are not using this at present.
* **(ii) cri-o** 
* **(iii) containerd**

*(Note: We can use both of these.)*

---

## (8) Monolithic and Microservices

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

---

## (9) Nginx Deployment Component

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

## (10) Environment Setup: VM, Ubuntu, and Runtime Installation

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

---

## (11) Key Benefits Summary

### Why Container Architecture Matters

**For Businesses:**
- **Cost Reduction:** Less hardware needed compared to separate physical servers
- **Faster Deployment:** Applications can be deployed in seconds instead of hours
- **Better Resource Utilization:** One server can run many more applications
- **Reduced Downtime:** Microservices allow updates without stopping entire system

**For Developers:**
- **Consistent Environment:** "It works on my machine" problem solved
- **Easy Sharing:** Package entire application with all dependencies
- **Version Control:** Keep multiple versions of applications safely
- **Isolation:** Applications don't interfere with each other

**For Operations Teams:**
- **Easier Management:** Centralized control through container runtime
- **Security:** Bastion nodes provide controlled access
- **Scalability:** Easy to add more application instances
- **Monitoring:** Clear separation makes troubleshooting easier

### Real-World Impact

**Traditional Approach Problems:**
- 20 applications = 20 physical servers = High cost
- One application update = Entire server downtime
- Resource waste: Most servers only use 10-15% capacity

**Container Architecture Benefits:**
- 20 applications = 2-3 servers = 70% cost savings
- Individual application updates = No downtime for others
- Better resource usage: 70-80% server capacity utilized

### Technology Comparison

| Aspect | Physical Servers | Virtual Machines | Containers |
|--------|-----------------|------------------|------------|
| **Setup Time** | Hours/Days | 10-30 minutes | 1-2 seconds |
| **Resource Overhead** | None | High (4GB+ per VM) | Minimal (5-50MB) |
| **Isolation** | Complete | Complete | Process-level |
| **Portability** | None | Limited | Excellent |
| **Density** | 1 app per server | 5-10 VMs per server | 50+ containers per server |

---

## Summary

This document covered the complete container architecture ecosystem:

**Core Components:**
1. **Container Host** - Server that runs containers using container runtime
2. **Container Client** - Secure access method using bastion nodes for production safety
3. **Container Images** - Lightweight packages containing OS (50MB) + application + libraries
4. **Container Registries** - Storage systems for images (public like Docker Hub, or private)
5. **Container Namespaces** - Isolation technology giving each container separate processes, files, network
6. **Container** - Running instance of an image, behaves like any application process
7. **Container Runtime** - Two-layer system (high-level: containerd/cri-o, low-level: runc)
8. **Architecture Patterns** - Monolithic (all-in-one) vs Microservices (separate containers)
9. **Nginx Component** - High-performance web server and traffic router for containerized apps
10. **Environment Setup** - VM + Ubuntu + Docker installation for development

**Key Learning Points:**
- Containers solve the "dependency conflict" problem that made running multiple apps difficult
- Microservices architecture prevents business loss from application downtime
- Container technology enables modern scalable applications used by Netflix, Google, Amazon
- Proper setup and understanding of each component is essential for successful implementation

**This Architecture Enables:**
- Modern application deployment patterns
- Efficient resource utilization
- Independent scaling of application components
- Zero-downtime updates and deployments