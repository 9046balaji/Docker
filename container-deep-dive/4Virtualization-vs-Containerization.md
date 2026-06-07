<div align="center">
  <h1>⚖️ Virtualization vs Containerization</h1>
  <p><strong>Comparing physical servers, virtual machines, and containers for application deployment</strong></p>
  <img src="https://img.shields.io/badge/Level-Intermediate-yellow?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-15%20minutes-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Part-4%20of%206-orange?style=flat-square" />
</div>

---

## 📚 Table of Contents

- [Physical Servers](#-physical-servers)
- [Virtual Machines](#-virtual-machine-hypervisor)
- [Containers](#-containers)
- [What is a Container?](#-what-is-a-container)
- [Containers vs Virtual Machines](#-containers-vs-virtual-machines)
- [Why Containers are Lightweight](#-why-containers-are-lightweight)
- [Container File System Structure](#-files-and-folders-in-container-base-images)
- [Host OS Integration](#-files-and-folders-that-containers-use-from-the-host-os)
- [Summary Comparison](#-summary-comparison)

---

This document compares different approaches to running applications: Physical Servers, Virtual Machines, and Containers.

---

## 🖥️ Physical Servers

In the traditional physical server approach:

1. **Single Application Limitation:** We need to buy a server with 100 GB RAM, 100 CPUs, etc., but we can only run one application on it.

2. **Resource Waste:** Because of this limitation, we are not using the full resources of the server. We end up wasting RAM and CPU capacity.

3. **Low Efficiency:** This results in poor resource utilization and high costs per application.

> [!NOTE]
> Physical servers typically utilize only 5–15% of their total capacity, making them the least cost-effective option for running applications.

---

## 🖥️ Virtual Machine (Hypervisor)

Virtualization technology addresses the limitations of physical servers:

1. **Virtual Server Creation:** Virtualization is used to create virtual servers on top of physical servers.

2. **Separate Operating Systems:** In this setup, we have a separate guest OS for every single virtual machine.

3. **Application Isolation:** On top of each guest OS, we run our application.

4. **Multiple Applications:** Instead of running just a single application on a physical server, we can create multiple VMs and run multiple applications simultaneously.

5. **Security Benefits:** VMs are very secure even though they run on a single physical server, because there is a completely separate OS for every application.

```text
       25          25          25          25  <-- (Resource Division)
   .___________.___________.___________.___________. 
  |   App 1   |   App 2   |   App 3   |   App 4   |
  |___________|___________|___________|___________|
  |  Guest OS |  Guest OS |  Guest OS |  Guest OS |
  |___________|___________|___________|___________|   Total Resources:
  |                                               |   * 100 GB RAM
  |                  Hypervisor                   |   * 100 CPU
  |_______________________________________________|
  |                                               |
  |                Physical Server                |
  |_______________________________________________|
```

### ⚠️ Virtual Machine Limitations

6. **Underutilization Problem:** Even though we split the physical server and run multiple applications instead of just one, we are still not fully utilizing the resources of each VM.

7. **Financial Impact:** This underutilization causes heavy financial losses to organizations.

8. **Root Cause:** This happens because every single VM must run a full, heavy guest operating system.

> [!WARNING]
> Each VM requires a minimum of 4–8 GB of RAM just for the guest operating system, regardless of how little the actual application needs. This creates massive resource overhead across large-scale deployments.

---

## 🐳 Containers

Containers provide a more efficient solution to application deployment and resource utilization.

### Model 1: Bare Metal Deployment (On-Premise)

```text
 ___________________________________________
| C1 (Minimal OS) |   C2   |   C3   |   C4   |
|===========================================|
|                  Docker                   |
|===========================================|
|             Host Operating System         |
|===========================================|
|                 HP-Server                 |
|___________________________________________|
```

### Model 2: Cloud Provider Deployment (Virtualized)

```text
 ___________________________________________
| C1 (Minimal OS) |   C2   |   C3   |   C4   |   C5   |
|--------.--------|--------|--------|--------|--------|
|        | M      |        |        |        |        |
|=====================================================|
|                       Docker                        |
|=====================================================|
|                     VM / EC2                        |
|=====================================================|
|          Physical Server (Cloud Providers)          |
|_____________________________________________________|
```

### Container Characteristics

1. **Lightweight Design:** Containers are **lightweight** because they do not contain a full, heavy operating system.

2. **Resource Sharing:** Instead, containers pull resources directly from the underlying virtual machine or physical server they are running on. They share the libraries and dependencies of the **Host Operating System**.

3. **Standard Package:** A container is simply a standard package containing:

   **Container Package = Application + Libraries + System Dependencies**

4. **Portability:** Because of this design, they are incredibly easy to ship, deploy, and move across different environments.

> [!IMPORTANT]
> Containers are **ephemeral** in nature, meaning they are short-lived. A container does not have its own permanent file system because it is lightweight.
> 
> For example, if an Nginx container goes down or crashes, all of its internal log files get permanently deleted. Containers rely purely on the host system or external volumes to save data permanently.

---

## 📦 What is a Container?

A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.

A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries, and settings.

---

## 🆚 Containers vs. Virtual Machines

Containers and virtual machines are both technologies used to isolate applications and their dependencies, but they have some key differences.

```text
       CONTAINERS ARCHITECTURE               VIRTUAL MACHINES ARCHITECTURE
     .---------------------------.            .---------------------------.
    |  App 1  |  App 2  |  App 3  |          |  App 1  |  App 2  |  App 3  |
    |---------'---------'---------|          |---------'---------'---------|
    |    Container Engine/Docker  |          | G. OS 1 | G. OS 2 | G. OS 3 |
    |-----------------------------|          |---------'---------'---------|
    |          Host OS            |          |         Hypervisor          |
    |-----------------------------|          |-----------------------------|
    |       Physical Server       |          |       Physical Server       |
     '---------------------------'            '---------------------------'
```

### 📊 Resource Utilization

**Containers:** Share the host operating system kernel, making them significantly lighter and faster than VMs.

**VMs:** Have a full-fledged guest OS and run on top of a hypervisor, making them much more resource-intensive.

### 🔄 Portability

**Containers:** Designed to be highly portable. They can run smoothly on any system that has a compatible host operating system.

**VMs:** Less portable because they require a specific compatible hypervisor layer to be installed on the destination environment to run.

### 🔒 Security

**Containers:** Share the host kernel, which means they have slightly lower isolation boundaries compared to VMs.

**VMs:** Provide a higher level of security because each VM has its own independent operating system, providing a completely isolated environment from the host and other VMs.

> [!NOTE]
> Containers provide slightly less isolation, as they share the host operating system kernel. For workloads requiring maximum isolation, VMs or hardened container runtimes like <kbd>gVisor</kbd> or <kbd>Kata Containers</kbd> may be preferred.

### ⚙️ Management

**Containers:** Managing containers is typically much easier than managing VMs because containers are designed to be lightweight and fast-moving.

**VMs:** Managing VMs involves higher overhead because you have to maintain, patch, and update the full guest operating system for every single instance.

---

## 🪶 Why Containers are Lightweight

Containers are lightweight because they use a technology called **containerization**, which allows them to share the host operating system's kernel and libraries while still providing isolation for the application and its dependencies.

This results in a much smaller footprint compared to traditional virtual machines, as the containers do not need to pack a full operating system. Additionally, Docker containers are designed to be minimal — only including what is strictly necessary for the application to run, which further reduces their size.

---

## 📂 Files and Folders in Container Base Images

Inside a standard container base image, you will find a minimal Linux directory structure:

```text
 / (Root Directory)
 ├── bin/   --> User binary executable files (e.g., ls, cp, mkdir)
 ├── sbin/  --> System binary executable files (e.g., init, shutdown)
 ├── etc/   --> Contains configuration files for various system services
 ├── lib/   --> Contains shared library files used by the binary executables
 ├── usr/   --> User-related files (shared applications, libraries, and docs)
 ├── var/   --> Contains variable data that changes constantly (log files, temp files)
 └── root/  --> The dedicated home directory of the root (admin) user
```

---

## 🔗 Files and Folders that Containers Use from the Host OS

```text
 .--------------------------------------------------------.
|                   RUNNING CONTAINER                    |
|  [ Isolated Processes ]      [ App Code & Libraries ]  |
 '-----------+----------------------------+--------------'
             |                            |
             | (System Calls)             | (Namespaces & cgroups)
             v                            v
 ________________________________________________________
|                        HOST OS                         |
|  ====================================================  |
|       Linux Kernel (Handles Syscalls, CPU, RAM, I/O)  |
|  ====================================================  |
|   [ Namespaces ]   [ cgroups ]   [ Bind Mounts ]      |
|   (Isolation)      (Resource     (Host File           |
|                    Limiting)      System)             |
|________________________________________________________|
```

### 📁 Host File System Access

Docker containers can access the host file system using **bind mounts**, which allow the container to read and write files directly in the host file system.

### 🌐 Networking Stack

The host's networking stack is used to provide network connectivity to the containers. Docker containers can be connected to the host's network directly or through a virtual network bridge.

### 🔧 System Calls

The host's kernel handles all system calls coming from the container. This is exactly how the container accesses the underlying host's hardware resources, such as CPU, memory, and storage I/O.

### 🔒 Namespaces

Docker containers use **Linux namespaces** to create highly isolated environments for the container's processes. Namespaces provide clean isolation for crucial resources such as:
- File system mounts
- Process IDs (PID) 
- Network interfaces

### ⚖️ Control Groups (cgroups)

Docker containers use **cgroups** to limit and control the exact amount of hardware resources that a specific container can access. This includes:
- CPU usage
- Memory allocation
- Disk I/O

> [!IMPORTANT]
> Control groups prevent any single container from consuming all host resources and crashing the whole system. This is a critical safety mechanism in multi-container environments.

### 🛡️ Important Isolation Notes

It is important to note that while a container uses resources directly from the host OS, it is still completely isolated from both the host and other containers. Therefore, any changes made inside the container do not affect the host or other running containers.

> [!TIP]
> There are multiple ways to reduce your VM image size, including using minimal base images, multi-stage builds, and distroless images (covered in Chapter 6).

---

## 📊 Summary Comparison

| Aspect | Physical Servers | Virtual Machines | Containers |
|--------|-----------------|------------------|------------|
| **Applications per Server** | 1 | 4–10 | 50–200+ |
| **Resource Utilization** | 5–15% | 30–50% | 70–85% |
| **OS Overhead** | None | 4–8 GB per VM | Shared (50–100 MB total) |
| **Startup Time** | Minutes | 1–3 minutes | 1–2 seconds |
| **Isolation Level** | Complete | Complete | Process-level |
| **Portability** | None | Limited | High |
| **Cost Efficiency** | Low | Medium | High |
| **Management Complexity** | High | Medium | Low |

> [!NOTE]
> **Key Takeaway:** The evolution from Physical Servers → VMs → Containers represents increasingly efficient resource utilization while maintaining application isolation.

---

<div align="center">

| ⬅️ Previous | 🏠 Home | Next ➡️ |
|:---:|:---:|:---:|
| [Container Architecture & Isolation](./3Container-Architecture-and-Isolation.md) | [README](../README.md) | [Docker Engine, Storage & Networking](./5Docker-Engine-Storage-and-Networking.md) |

</div>