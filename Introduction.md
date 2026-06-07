# Server Provisioning & Infrastructure Evolution

This document details the evolution from traditional server provisioning to modern containerization, covering virtualization technologies and their business impact.

---

## 🏗️ Initial Infrastructure Setup

### Basic Server Configuration

```text
                     ( server provisioning )

     /---------\
    /           \              HP-server  -->  192.168.77.22            / \
   |   Hotspot   |----\                         (server ip)            /   \
    \           /      \                                              | TP- |
     \---------/        \                                              \Link/
                         \                                              \ /
   +---------------+   +--V---------------------------------+            |
   |    2 CPUS     |   |  /---------\                       |------------+
   |    5GB Ram    |   | /           \                      |            |
   |    500GB      |   | |  Hotspot  |                      |            v
   |   Python 3.7  |   | \           /                      |    +-------------------+   convert to ip
   +---------------+   |  \---------/                       |    | +---------------+ |   [using DNS]
                       |       |                            |    | | www.hotstar.com | |----> 192.168.77.22
                       |  +-------------------------------+ |    | +---------------+ |
                       |  |           Python 3.7          | |    +-------------------+
                       |  +-------------------------------+ |
                       |  |            RHEL-OS            | |
                       |  +-------------------------------+ |
                       +------------------------------------+
```

### DNS Resolution Process

When users access the deployed application via web browser, the traffic follows this path:

1. **User Input:** User types www.hotstar.com into their browser
2. **DNS Lookup:** System uses DNS to convert domain name to IP address: 192.168.77.22
3. **Gateway Routing:** Traffic routes through local network router/Wi-Fi to internet gateway
4. **Server Connection:** Gateway connects to backend host server, which returns page data to browser
---

## 🚀 Business Growth & Infrastructure Challenges

### The Multi-Application Problem

As companies grow, they develop additional applications with different requirements. Consider the **Prime** application that requires:

- **Hardware:** 4 CPUs, 10 GB RAM
- **Runtime:** Python 3.11 (vs existing Python 3.7)

```text
                     Company Growth Phase

   (Hotspot)-----------------------------\
       |                                  v
       v                     +----------------------------+               ( Prime )
+--------------+             | HP-server    192.168.77.22 |                   |
|    2 CPU     |             |                            |                   v
|   5 GB RAM   |             |         (Hotspot)          |           +-----------------+
|    500 GB    |             |             |              |           |     4 CPUS      |
|  Python 3.7  |             |  +----------------------+  |           |    10 GB RAM    |
+--------------+             |  |      Python 3.7      |  |           |   Python 3.11   |
                             |  +----------------------+  |           +-----------------+
                             |  |       RHEL-OS        |  |
                             |  +----------------------+  |
                             +----------------------------+
```

### The Runtime Conflict

Physical servers typically have high-end configurations (64+ CPU cores, 64-128 GB RAM). While there's sufficient capacity for both applications, a critical problem emerges when installing them on the same operating system:

```text
       ( H )-------\                                                    /-------( P )
         |          \          +------------------------------------+  /          |
         v           \--->     | HP-server      192.168.77.22       | <           v
   +------------+              |                                    |       +------------+
   |  p-3.7     |              |     ( H )                ( P )     |       |   4 CPU    |
   +------------+              |                                    |       |   p-3.11   |
                               |  +-------------------------------+ |       +------------+
                               |  |          Python 3.11          | |
                               |  +-------------------------------+ |
                               |  |            RHEL-OS             | |
                               |  +-------------------------------+ |
                               +------------------------------------+
```

**The Core Issue:** If you upgrade Python 3.7 to Python 3.11 to support Prime, the Hotspot application will break completely due to:
- Different software dependencies and libraries
- Incompatible versions that cannot coexist safely in a single OS instance

### Traditional Solution: Separate Physical Servers

The original approach was purchasing dedicated servers for each application:

- 2 apps = 2 servers
- 100 apps = 100 servers

```text
+---------------------------------------+       +---------------------------------------+
|               SERVER 1                |       |               SERVER 2                |
+---------------------------------------+       +---------------------------------------+
|  App: Hotstar                         |       |  App: Prime                           |
|  Runtime: Python 3.7                  |       |  Runtime: Python 3.11                 |
|  OS: RHEL-OS                          |       |  OS: RHEL-OS                          |
+---------------------------------------+       +---------------------------------------+
```

### Scaling Challenges

**Infrastructure Growth Problems:**
1. **Procurement vs Management:** Purchasing 100 VMs isn't the main issue
2. **Maintenance Overhead:** Managing 100 separate VMs creates massive operational burden:
   - Server health monitoring and uptime status
   - Physical environment management (cooling, temperature)
   - Resource allocation tracking (RAM, CPU utilization)

**Financial Impact:** The 1:1 application-to-server model results in:
- High upfront hardware investment
- Significant operational costs
- Substantial financial loss due to inefficient resource utilization

---

## 💡 Virtualization Solutions

### Type-2 Hypervisor: Software-Based Virtualization

To solve runtime conflicts, virtualization technology enables multiple isolated environments on a single physical server:

```text
                     VMware Workstation Solution

     +----------------------------- HP-server -----------------------------+
     |                                                                     |
     |   +-------------------- vmware workstation --------------------+    |
     |   |                                                            |    |
     |   |   +-----------+     +-----------+     +-----------+        |    |   +----------------+
     |   |   |    vm1    |     |    vm2    |     |    vm3    |        |    |   | configurations |
     |   |   |   ( H )   |     |   ( P )   |     |           |--------+----+-->|  RAM           |
     |   |   |           |     |           |     |           |        |    |   |  CPU           |
     |   |   | +-------+ |     | +-------+ |     |           |        |    |   |  HDD           |
     |   |   | | p-3.7 | |     | | p-3.11| |     |           |        |    |   +----------------+
     |   |   | +-------+ |     | +-------+ |     | +-------+ |        |    |
     |   |   | |ReL-os | |     | |ReL-os | |     | |ReL-os | |        |    |
     |   |   | +-------+ |     | +-------+ |     | +-------+ |        |    |
     |   |   +-----------+     +-----------+     +-----------+        |    |
     |   +------------------------------------------------------------+    |
     |                                                                     |
     |   +------------------------------------------------------------+    |
     |   |                           WIN-11                           |    |
     |   +------------------------------------------------------------+    |
     +---------------------------------------------------------------------+
```

**Implementation Process:**
1. Install base OS (Windows 11) on HP server
2. Install VMware Workstation software
3. Configure VM specifications (RAM, CPU, HDD allocation)
4. Create VMs and install guest operating systems
5. Deploy applications with their specific runtime requirements

**Popular Type-2 Hypervisors:**
- **VMware Workstation** (paid, enterprise-grade)
- **VirtualBox** (Oracle, free alternative)

**Type-2 Characteristics:**
- Software application installed on top of host OS
- Easier to use and manage
- Limited by host OS resources
- VM quantity constrained by main server capacity

### Type-1 Hypervisor: Bare-Metal Virtualization

Enterprise environments use Type-1 hypervisors for better performance and management:

```text
                              TYPE-1 HYPERVISOR

     +----------------------------- HP-server -----------------------------+
     |                                                                     |
     |   +-----------+         +-----------+         +-----------+         |
     |   |           |         |           |         |           |         |
     |   |    VM     |         |    VM     |         |    VM     |         |
     |   |           |         |           |         |           |         |
     |   +-----------+         +-----------+         +-----------+         |
     |                                                                     |
     |   +-------------------------------------------------------------+   |
     |   |                           VCENTER                           |   |
     |   +-------------------------------------------------------------+   |
     |                                                                     |
     |   +-------------------------------------------------------------+   |
     |   |                      Hypervisor (ESXi)                     |   |
     |   +-------------------------------------------------------------+   |
     +---------------------------------------------------------------------+
```

**Components:**
1. **ESXi** - Bare-metal hypervisor that runs directly on hardware
2. **vCenter** - Management platform for monitoring and controlling VMs

**Advantages:**
- Direct hardware access for better performance
- Centralized management of hundreds/thousands of VMs
- Advanced monitoring and access control
- Network management capabilities

### Limitations of Virtual Machines

**Resource Overhead Problem:**
- Application may need only 50 MB RAM to run
- VM requires minimum 4 GB RAM just for guest OS
- Massive resource wastage across multiple VMs
- Example: 100 VMs × 4 GB = 400 GB wasted on OS overhead alone

**Efficiency Impact:**
- Could theoretically create 200 VMs instead of 100 with better resource utilization
- This inefficiency led to development of containerization technology

---

## 🐳 Containerization Technology

### Container Runtime Architecture

Containerization solves VM resource wastage by sharing the host OS kernel while maintaining application isolation:

```text
                             HP-server

     +-----------------------------------------------------------------+
     |                                                                 |
     |   +-----+     +-----+     +-----+     +-----+                   |
     |   | C1  |     | C2  |     | C3  |     | C4  |                   |
     |   +-----+     +-----+     +-----+     +-----+                   |
     |                                                                 |
     |   +-----+     +-----+     +-----+     +-----+                   |
     |   | C5  |     | C6  |     | C7  |     | C8  |                   |
     |   +-----+     +-----+     +-----+     +-----+                   |
     |                                                                 |
     |   +---------------------------------------------------------+   |
     |   |                    Container Runtime                    |   |
     |   +---------------------------------------------------------+   |
     |   |                         RHEL-OS                         |   |
     |   +---------------------------------------------------------+   |
     +-----------------------------------------------------------------+
```

**Key Differences from VMs:**
- **Shared OS:** All containers share the host operating system kernel
- **Lightweight:** No need for separate guest OS in each container
- **Resource Efficient:** Container uses minimal RAM (as low as 4 MB vs 4 GB for VM)
- **Fast Startup:** Containers start in seconds vs minutes for VMs

### Container Images

Containers are created from **container images** that package applications with minimal overhead:

```text
       .-----------.
     /   Light-    |   App-      \     
    /    weight    |  lication    \    • Traditional OS: ~16 GB
   |       OS      |  Libraries   |    • Lightweight OS: ~50 MB  
    \              |              /    • Minimal resource usage
     \             |             /     • Optimized for containers
       '-----------'
```

**Image Components:**
- **Lightweight OS layer** (typically 50 MB vs 16 GB for full OS)
- **Application code and dependencies**
- **Runtime libraries and configurations**
- **Application-specific environment settings**

**Container Creation Process:**
1. Build container image with application and lightweight OS
2. Deploy image to container runtime
3. Run container instances from the image
4. Each container runs isolated application processes

### Kubernetes: Container Orchestration

```text
 _________          .-----------------------.      .-----------------------.
|         |        |  ( ) ( ) ( ) ( ) ( )  |      |  ( ) ( ) ( ) ( ) ( )  |
|         |--------|  ( ) ( ) ( ) ( )      |      |  ( ) ( ) ( ) ( ) ( )  |
|         |        |=======================|      |=======================|
|  K8s    |        |   container-runtime   |      |          cr           |
|         |--------|=======================|      |=======================|
|         | \      |          OS           |      |          OS           |
|_________|  \     '-----------------------'      '-----------------------'
              \     .-----------------------.      .-----------------------.
               \   |  ( ) ( ) ( ) ( ) ( )  |      |  ( ) ( ) ( ) ( ) ( )  |
                \  |  ( ) ( ) ( )          |      |  ( ) ( ) ( ) ( )      |
                 '-|=======================|      |=======================|
                   |          cr           |      |          cr           |
                   |=======================|      |=======================|
                   |          OS           |      |          OS           |
                   '-----------------------'      '-----------------------'
```

**Kubernetes (K8s) Capabilities:**
- **Multi-server Management:** Centrally manages containers across multiple servers
- **Auto-healing:** Automatically recreates failed containers
- **Load Distribution:** Distributes containers across available resources
- **Scaling:** Automatically scales applications based on demand
- **Service Discovery:** Manages communication between containers
- **Rolling Updates:** Enables zero-downtime application updates

**Container Orchestration Benefits:**
- High availability through automatic failover
- Efficient resource utilization across cluster
- Simplified deployment and management
- Built-in monitoring and logging capabilities

---

## 📚 Related Topics for Further Study

### Container Architecture Components:
1. **Container Host:** Physical or virtual machine running container runtime
2. **Container Client:** CLI tools and APIs for managing containers
3. **Container Images:** Immutable templates for creating containers
4. **Container Registries:** Repositories for storing and distributing images
5. **Container Namespaces:** Isolation mechanisms for process, network, and filesystem
6. **Containers:** Running instances of container images
7. **Container Runtime Plugins:** Extensions for networking, storage, and security

### Architecture Patterns:
8. **Monolithic vs Microservices:** Application design approaches
9. **Nginx:** Web server and reverse proxy configuration for container environments

### Practical Implementation:
10. **VM Setup:** Installing Ubuntu VM for container runtime setup and Docker deployment

---

## 🎯 Summary

This document covered the evolution from traditional server provisioning to modern containerization:

1. **Initial Challenge:** Runtime conflicts between applications on shared servers
2. **Virtualization Solution:** VMs provided isolation but with significant resource overhead
3. **Containerization Advantage:** Shared OS kernel approach dramatically reduces resource waste
4. **Orchestration:** Kubernetes enables enterprise-scale container management

The progression from physical servers → virtual machines → containers represents increasingly efficient resource utilization while maintaining application isolation and deployment flexibility.
