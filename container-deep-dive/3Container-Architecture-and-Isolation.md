<div align="center">
  <h1>🏗️ Container Architecture and Isolation</h1>
  <p><strong>Key components of container architecture and how they provide isolation for applications</strong></p>
  <img src="https://img.shields.io/badge/Level-Intermediate-yellow?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-20%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Part-3%20of%206-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

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
* **(ii)** If any user wants to access a container — for example, container (C):
* **(iii)** To run that C-container, there will be certain commands.
* **Example:** <kbd>connect_c-container</kbd>

Automatically, the VM directly connects to the container host, and then we get access to that specific container.

> [!IMPORTANT]
> We can only use container-related commands, and users are only given access to certain dedicated containers. We do not give access to all the containers present on that container host. All others are restricted.

> [!NOTE]
> We are not using this approach now. It has been replaced by more modern access control methods.

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

> [!TIP]
> Think of a container registry like an "App Store" for Docker images. You push your images there, and anyone with access can pull and run them.

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

For every container, the system creates a separate:
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

> [!WARNING]
> Ending a container process in Task Manager will immediately stop the container, just like closing any other application. Be careful not to terminate container processes accidentally in production.

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
* **(i)** <kbd>dockerd</kbd> → We are not using this at present.
* **(ii)** <kbd>cri-o</kbd>
* **(iii)** <kbd>containerd</kbd>

> [!NOTE]
> We can use both <kbd>cri-o</kbd> and <kbd>containerd</kbd> as high-level container runtimes. The choice depends on the orchestration platform and organizational preferences.

---

## (8) Monolithic and Microservices

### ❌ Monolithic Architecture

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

(2) But since we are running all three bookings inside a single container, all three bookings will stop if we try to update even one of them.

(3) If there is any update for flight booking, we again have to stop the entire server.

> [!CAUTION]
> While updating one feature, if users want to access the other two, they will also be down. Users will go to another source like BookMyShow, and the business will face a loss. This is the fundamental problem with **monolithic architecture**.

### ✅ Microservices Architecture

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

* We host everything separately. So, only the one being updated will be down.
* The remaining services will work perfectly.
* Dedicated features are running in dedicated containers, so there is less downtime and it will not affect other features.

> [!TIP]
> After Kubernetes came into the picture, even individual service updates can happen with **zero downtime** using rolling updates.

---

## (9) Nginx Deployment Component

* Nginx is a high-performance web server, load balancer, and reverse proxy engine.
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
* Once the Ubuntu OS is up and running, we open the terminal and install the container runtime engine step by step using these sequential commands:

**Step 1: Update the local package tracking repository list**

```bash
# ✅ Refresh the package index
sudo apt update
```

**Step 2: Download and install the core Docker engine utilities**

```bash
# 📦 Install Docker
sudo apt install docker.io -y
```

**Step 3: Enable and instantly start the Docker background service**

```bash
# 🚀 Enable and start Docker
sudo systemctl enable --now docker
```

> [!NOTE]
> After running these three commands, Docker will be installed and running on your Ubuntu VM. You can verify the installation by running <kbd>docker --version</kbd>.

---

## (11) Key Benefits Summary

### Why Container Architecture Matters

<details>
<summary>💼 For Businesses</summary>

- **Cost Reduction:** Less hardware needed compared to separate physical servers
- **Faster Deployment:** Applications can be deployed in seconds instead of hours
- **Better Resource Utilization:** One server can run many more applications
- **Reduced Downtime:** Microservices allow updates without stopping the entire system

</details>

<details>
<summary>👨‍💻 For Developers</summary>

- **Consistent Environment:** "It works on my machine" problem solved
- **Easy Sharing:** Package the entire application with all dependencies
- **Version Control:** Keep multiple versions of applications safely
- **Isolation:** Applications don't interfere with each other

</details>

<details>
<summary>🔧 For Operations Teams</summary>

- **Easier Management:** Centralized control through container runtime
- **Security:** Bastion nodes provide controlled access
- **Scalability:** Easy to add more application instances
- **Monitoring:** Clear separation makes troubleshooting easier

</details>

### Real-World Impact

**Traditional Approach Problems:**
- 20 applications = 20 physical servers = High cost
- One application update = Entire server downtime
- Resource waste: Most servers only use 10–15% capacity

**Container Architecture Benefits:**
- 20 applications = 2–3 servers = 70% cost savings
- Individual application updates = No downtime for others
- Better resource usage: 70–80% server capacity utilized

### 📊 Technology Comparison

| Aspect | Physical Servers | Virtual Machines | Containers |
|--------|-----------------|------------------|------------|
| **Setup Time** | Hours/Days | 10–30 minutes | 1–2 seconds |
| **Resource Overhead** | None | High (4 GB+ per VM) | Minimal (5–50 MB) |
| **Isolation** | Complete | Complete | Process-level |
| **Portability** | None | Limited | Excellent |
| **Density** | 1 app per server | 5–10 VMs per server | 50+ containers per server |

---

## 📝 Summary

This document covered the complete container architecture ecosystem:

<details>
<summary>⚡ Quick Reference — All Core Components</summary>

| # | Component | Description |
|---|-----------|-------------|
| 1 | **Container Host** | Server that runs containers using container runtime |
| 2 | **Container Client** | Secure access method using bastion nodes for production safety |
| 3 | **Container Images** | Lightweight packages containing OS (50 MB) + application + libraries |
| 4 | **Container Registries** | Storage systems for images (public like Docker Hub, or private) |
| 5 | **Container Namespaces** | Isolation technology giving each container separate processes, files, and network |
| 6 | **Container** | Running instance of an image; behaves like any application process |
| 7 | **Container Runtime** | Two-layer system (high-level: containerd/cri-o, low-level: runc) |
| 8 | **Architecture Patterns** | Monolithic (all-in-one) vs Microservices (separate containers) |
| 9 | **Nginx Component** | High-performance web server and traffic router for containerized apps |
| 10 | **Environment Setup** | VM + Ubuntu + Docker installation for development |

</details>

**Key Learning Points:**
- Containers solve the "dependency conflict" problem that made running multiple apps difficult
- Microservices architecture prevents business loss from application downtime
- Container technology enables modern scalable applications used by Netflix, Google, and Amazon
- Proper setup and understanding of each component is essential for successful implementation

**This Architecture Enables:**
- Modern application deployment patterns
- Efficient resource utilization
- Independent scaling of application components
- Zero-downtime updates and deployments

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Part 2: Containerization](../introduction/Part-2-Containerization.md) | [README](../README.md) | [Virtualization vs Containerization](./4Virtualization-vs-Containerization.md) |

</div>