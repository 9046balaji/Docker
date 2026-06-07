# Server Provisioning & Network Mapping

This document details the environment topology and DNS routing layout as specified by the server provisioning team.

---

## 🏗️ Infrastructure & Network Layout

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
                       |       |                            |    | |[www.hotstar.com](https://www.hotstar.com)| |----> 192.168.77.22
                       |  +-------------------------------+ |    | +---------------+ |
                       |  |           Python 3.7          | |    +-------------------+
                       |  +-------------------------------+ |
                       |  |            RHEL-OS            | |
                       |  +-------------------------------+ |
                       +------------------------------------+


When users attempt to access the deployed application via their web browser, the traffic follows a multi-step resolution path:

* **User Input:** The user types www.hotstar.com (or www.hotspot.com) into their browser.
* **DNS Lookup:** Since the address is not present locally, the system uses DNS (Domain Name System) to handle name-to-IP conversion. The domain is converted directly to the destination IP address: 192.168.77.22.
* **Gateway Routing:** The traffic goes to the local network router/Wi-Fi and is redirected to the active network gateway connected to the internet.
* **Server Connection:** Through the internet, the gateway connects to the application running on the backend host server, which returns the page data to display in the browser.
# Infrastructure Evolution: Business Progression Phase

This document captures the structural architecture and environment provisioning state during the company's growth phase, highlighting the introduction of the new application tier.

---

## The Growth Problem: Version Mismatches

[cite_start]As the company progresses, they build a second application called **Prime**[cite: 887, 889]. 
[cite_start]The Prime application is heavier and requires a newer environment[cite: 897, 898, 899]:
* [cite_start]**Requirements:** 4 CPUs, 10 GB RAM, and **Python 3.11** [cite: 897, 898, 899]

## 🏗️ Architectural Diagram

```text
                     Now the company is progressing

   (Hotspot)-----------------------------\
       |                                  v
       v                     +----------------------------+               ( prime )
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

### The Conflict
[cite_start]Physical servers usually have high-end configurations (like 64 CPU cores and 64 GB or 128 GB of RAM)[cite: 901, 902]. [cite_start]There is plenty of space on the server to hold both apps, but a major problem arises if you try to install them together on the same operating system[cite: 913, 915, 917]:

# Infrastructure Conflict: Shared Runtime Mismatch

This document captures the system state where a shared host architecture breaks due to runtime dependency upgrades, specifically illustrating the conflict between legacy and new application requirements on the same machine.

---

## 🏗️ System Layout & Breakdown Diagram

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
                               |  |            ReL-OS             | |
                               |  +-------------------------------+ |
                               +------------------------------------+

> [cite_start]If you upgrade or replace the existing Python 3.7 environment with Python 3.11 on that server to run Prime, the **Hotspot application will completely break and show errors**[cite: 917, 918, 921, 922]. 

[cite_start]This happens because the underlying software dependencies and libraries are completely different, and you cannot manage both conflicting versions safely inside a single operating system instance[cite: 925, 926, 928].

### The Old (Expensive) Solution
[cite_start]To solve this, companies used to buy completely separate physical servers for each application environment[cite: 933, 939]. 
* [cite_start]2 apps = 2 servers [cite: 938, 939]
* [cite_start]100 apps = 100 servers [cite: 943, 944]

+---------------------------------------+       +---------------------------------------+
|               SERVER 1                |       |               SERVER 2                |
+---------------------------------------+       +---------------------------------------+
|  App: Hotstar                         |       |  App: Prime                           |
|  Runtime: Python 3.7                  |       |  Runtime: Python 3.11                 |
|  OS: RHEL-OS                          |       |  OS: RHEL-OS                          |
+---------------------------------------+       +---------------------------------------+

## Infrastructure Scaling & Dependency Challenges

### 1. Initial Approach

* **Server Creation:** We will begin by creating two servers.
* **Dependency Issues:** When a library is updated or changed, version mismatch issues arise (e.g., a specific application strictly requires a certain version to function).
* **Application Mapping:** Because we originally only had two applications, we mapped them directly by deploying them across 2 dedicated servers.

### 2. The Problem with Scaling

* **Scenario:** For example, if the infrastructure grows to **100 applications**, following this model means we would need to purchase **100 servers (Virtual Machines)**.

#### The Core Technical Challenges:

1. **Procurement vs. Management:** Purchasing 100 VMs is not the primary issue.
2. **Maintenance Overhead:** The true challenge lies in the ongoing management and maintenance of 100 separate VMs. This includes continuously monitoring critical factors such as:
* Server health and uptime status.
* Physical environment constraints (cooling systems and temperature).
* Resource allocation (RAM utilization).



> ⚠️ **Conclusion:** Relying on a 1:1 application-to-server model introduces a massive operational burden and results in a significant financial loss due to the high upfront investment in hardware.

# Solution: Virtualization via Type-2 Hypervisor

This document outlines the technical architecture implemented to solve the runtime dependency conflict using Type-2 virtualization software deployed on the host system.

---

## 🏗️ Architectural Diagram

```text
                     To solve this there is one New Technology
                                 that is VM-ware

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

                              TYPE-2 HYPERVISER

1. In an HD-server, we install an OS, which is Windows 11.
2. In Windows 11, we install software called VMware Workstation.

* VMware $\rightarrow$ It is built by VMware (paid version).
* VirtualBox $\rightarrow$ It is built by Oracle.

(3) First, we have to configure the VMs for creation, specifying:

* RAM, CPU, HDD $\rightarrow$ How much capacity they should have.
* The VM is created but is empty.

(4) In that VM, we create another OS. In that OS, we install P-3.7, and on that, we will host the application.
(5) Like that, we can create multiple VMs.

* **Limitation:** We can only create as many VMs as the main server's capacity allows.

---

### Type-2 Hypervisor

(1) It is nothing but installing software on top of the OS, just like an application.
(2) To run our virtual machines, we install a tool on top of the OS. By using that tool, we create VMs. This is called a Type-2 hypervisor.

# Enterprise Architecture: Type-1 Hypervisor Deployment

This document maps out the enterprise-grade bare-metal virtualization stack, illustrating how isolated virtual instances are provisioned and centrally managed on top of physical hardware.

---

## 🏗️ Architectural Diagram

```text
                              TYPE-1 HYPER-VISER

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
     |   |                      Hyperviser (esxi)                      |   |
     |   +-------------------------------------------------------------+   |
     +---------------------------------------------------------------------+

(1) ESXi is an operating server.
(2) We can directly create VMs using this, but it is not recommended in companies because it comes with a CLI.

* We cannot easily manage or monitor them if there are a lot of VMs, like 100 or 1,000.
* CLI $\rightarrow$ It is a Command Line Interface, like `cmd`.

(3) So, we install a tool called vCenter.

* (i) From this, we can monitor all VMs.
* (ii) We can also monitor the networks of the VMs.
* (iii) We can manage access.

(4) It is very good for management.

* In this setup, the VMs are created using ESXi and are managed or maintained by vCenter.

---

### Type-1 Hypervisor

(Windows 11 is also an OS, but we cannot create VMs using just the standard Windows OS.)

### Using VMware

* For example, our application needs only 50 MB of RAM to run.
* Even so, we need to assign a minimum of 4 GB of RAM just to run the guest operating system (OS).
* Because of this, a lot of RAM is wasted.

**Example:**

* (i) If we create 100 VMs, we need to assign 4 GB of RAM for every single application.
* (ii) This leads to massive wastage.
* (iii) If we could prevent that wastage, instead of only 100 VMs, we could create another 100.

> **Note:** This problem is exactly why a new technology was developed, called **containerization**.

# Container Runtime Architecture

```text
                             HP-server

     +-----------------------------------------------------------------+
     |                                                                 |
     |   +-----+     +-----+     +-----+     +-----+                   |
     |   |     |     |     |     |     |     |     |                   |
     |   +-----+     +-----+     +-----+     +-----+                   |
     |                                                                 |
     |   +-----+     +-----+     +-----+     +-----+                   |
     |   |     |     |     |     |     |     |     |                   |
     |   +-----+     +-----+     +-----+     +-----+                   |
     |                                                                 |
     |   +---------------------------------------------------------+   |
     |   |                    container-runtime                    |   |
     |   +---------------------------------------------------------+   |
     |   |                         RHEL-OS                         |   |
     |   +---------------------------------------------------------+   |
     +-----------------------------------------------------------------+

* In VMware, we also create something similar, but the difference is that to run a container, we need an **IMAGE**.
* **Example:** To run Windows 11, we need the Windows 11 image.

### To create a container image:

```text
       .-----------.
     /   Light-  |   App-    \     * Windows 11 (OS) size = ~16 GB
    /    weight  |  lication  \    * Lightweight OS size = 50 MB
   |       OS    |  Libraries |    * So it can use less RAM,
    \            |            /       like even 4 MB of RAM.
     \           |           /
       '-----------'

```

* We create a container image by converting our application into a container image.
* If we run that container, then our application runs inside that container.

## Kubernetes

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

* K8s is a server that manages all the remaining servers as well as their containers.
* If any container goes down, K8s automatically re-creates that container.
* It is a container orchestration tool.

## Topics

### Container Architecture:

(i) What is a container host?
(ii) What is a container client?
(iii) Container images
(iv) Container registries
(v) Container namespaces
(vi) Containers
(vii) Container runtime plugins
(viii) Monolithic vs. Microservices
(xi) Nginx
(x) Installing a VM → Ubuntu → Container runtime
