<div align="center">
  <h1>🛠️ Docker Installation Guide</h1>
  <p><strong>Complete installation instructions for Docker on Windows, Linux, and macOS</strong></p>
  <img src="https://img.shields.io/badge/Level-Beginner-green?style=flat-square" />
  <img src="https://img.shields.io/badge/Read%20Time-10%20minutes-blue?style=flat-square" />
</div>

---

## 📚 Table of Contents

1. [Windows Installation](#-installation-on-windows)
2. [Docker Desktop (GUI Method)](#option-a-docker-desktop-recommended-gui-method)
3. [Bare-Metal Engine in WSL 2](#option-b-bare-metal-docker-engine-inside-wsl-2-no-desktop-gui)
4. [Docker Desktop (Step-by-Step Visual Guide)](#-docker-desktop-step-by-step-visual-guide)
5. [Ubuntu / Debian](#1-ubuntu--debian)
6. [CentOS / RHEL](#2-centos--rhel)
7. [macOS](#3-macos)
8. [Post-Installation Verification](#-post-installation-verification)

---

## 🪟 Installation on Windows

On Windows, you can choose between the full graphical interface with **Docker Desktop** or a lightweight, CLI-only engine running directly inside **Windows Subsystem for Linux (WSL 2)**.

```text
 _______________________________________________________________
|                     WINDOWS RUNTIME HYBRID                    |
|                                                               |
|    [ Windows Host ] <=====> [ WSL 2 Kernel Integration ]      |
|    (PowerShell / CMD)       (Ubuntu / Linux Distros)          |
|            |                           |                      |
|            v                           v                      |
|    [ Docker Desktop GUI ]      [ Docker Engine CLI ]          |
|_______________________________________________________________|
```

---

### Option A: Docker Desktop (Recommended GUI Method)

This installs the complete management software alongside the core container engine.

* **Step 1: Initialize the Windows Subsystem for Linux foundation layer**
  Open PowerShell as an Administrator and install or refresh WSL to its latest stable release:
  ```powershell
  wsl --install
  wsl --update
  ```

* **Step 2: Install via the Windows Package Manager (`winget`)**
  Run this command to download and install the software package silently:
  ```powershell
  winget install Docker.DockerDesktop
  ```

  > [!TIP]
  > Alternatively, you can manually download the `.exe` installer binary from the [official Docker website](https://www.docker.com/products/docker-desktop/).

* **Step 3: Establish WSL Distribution Integration**
  1. Launch **Docker Desktop** from the Windows Start Menu.
  2. Navigate to **Settings → General** and verify that **"Use the WSL 2 based engine"** is checked.
  3. Go to **Settings → Resources → WSL Integration**, toggle the switch next to your target distribution (e.g., `Ubuntu`), and click **Apply & Restart**.

---

### Option B: Bare-Metal Docker Engine inside WSL 2 (No Desktop GUI)

If you want to save resources and run Docker natively inside your WSL 2 terminal environment without the overhead of the Docker Desktop application:

* **Step 1: Open your Linux distribution terminal and remove legacy packages**
  ```bash
  sudo apt-get remove docker docker-engine docker.io containerd runc
  ```

* **Step 2: Download and execute the official Docker installation script**
  ```bash
  curl -fsSL https://get.docker.com -o get-docker.sh
  sudo sh get-docker.sh
  ```

* **Step 3: Add your user account to the Docker security group**
  ```bash
  sudo usermod -aG docker $USER
  ```

  > [!NOTE]
  > Close your current terminal session or run `newgrp docker` to apply the permission changes immediately.

* **Step 4: Enable the systemd daemon**
  Ensure your Linux subsystem starts systemd automatically. Add the following lines to your system configuration file (`/etc/wsl.conf`):
  ```ini
  [boot]
  systemd=true
  ```

  Finally, return to Windows PowerShell and restart the WSL backend to finalize the configuration:
  ```powershell
  wsl --shutdown
  ```

---

## 🖱️ Docker Desktop Step-by-Step Visual Guide

If you prefer the standard Windows approach — downloading a file, double-clicking it, and clicking through a visual installation wizard — follow these steps.

```text
 ___________________________________________________________________
|                     GRAPHICAL INSTALLATION FLOW                   |
|                                                                   |
|   [ Web Browser ]  --->  [ Double-Click ]  --->  [ Setup Wizard ] |
|   (Download .exe)        (Run Installer)         (Click "OK")     |
|                                                                v  |
|   [ Docker Desktop App ] <---------------------- [ PC Restart ]   |
|   (Accept Terms & Use)                                            |
|___________________________________________________________________|
```

### Step 1: Download the Application Installer

1. Open your web browser (Chrome, Edge, etc.) and go to the official website: **[docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/)**.
2. Click the blue button labeled **"Download for Windows"**.
3. Wait for the installer file (named something like `Docker Desktop Installer.exe`) to finish downloading to your **Downloads** folder.

### Step 2: Run the Installer

1. Open **File Explorer** and navigate to your **Downloads** folder.
2. Find the `Docker Desktop Installer` file.
3. **Double-click** it to start the installation.
4. If a Windows security prompt asks *"Do you want to allow this app to make changes to your device?"*, click **Yes**.

### Step 3: Click Through the Configuration Screen

A configuration window will appear with two checkboxes:

* **Use WSL 2 instead of Hyper-V (Recommended):** Ensure this is **checked** (it is usually checked by default). This allows Docker to run efficiently.
* **Add shortcut to desktop:** Leave this checked if you want a clickable icon on your desktop.
* Click the blue **OK** button at the bottom right.

### Step 4: Wait for Installation and Restart Your PC

1. The installer will unpack all files. A progress bar will appear — let it complete (approximately 2–3 minutes).
2. Once finished, you will see a button that says **"Close and restart"** or **"Close and log out"**.
3. Click that button. Your computer will restart to finalize the setup.

### Step 5: Launch and Use the Application

1. After your computer restarts, look for the whale icon named **Docker Desktop** on your desktop.
2. **Double-click** the Docker Desktop icon to open it.
3. A **Service Agreement** window will appear the first time you open it. Click the **Accept** button.
4. On the next screen, select **"Use recommended settings"** and click **Finish**.

> [!TIP]
> 🎉 **Success!** You will now see the main Docker Desktop dashboard. You can manage your containers, images, and settings entirely from this graphical interface — no command prompt required!

---

## 🐧 Installation on Linux

```text
 _______________________________________________________________
|                     DOCKER INSTALLATION FLOW                  |
|                                                               |
|   [ CLI Command ] ---> [ Package Manager ] ---> [ App Engine ]|
|                             |                         |       |
|            (apt / yum / brew)                         v       |
|                                               [ systemd Service ]
|                                               (Enables Daemon)|
|_______________________________________________________________|
```

---

### 1. Ubuntu / Debian

Open your terminal and run the following commands in sequence:

* **Step 1: Update the local package repository index**
  ```bash
  sudo apt update
  ```

* **Step 2: Install the core Docker runtime engine**
  ```bash
  sudo apt install docker.io -y
  ```

* **Step 3: Enable the Docker daemon and start it immediately**
  ```bash
  sudo systemctl enable --now docker
  ```

---

### 2. CentOS / RHEL

For Red Hat-based distributions, use the `yum` package manager:

* **Step 1: Install the Docker package**
  ```bash
  sudo yum install -y docker
  ```

* **Step 2: Enable and start the Docker service**
  ```bash
  sudo systemctl enable --now docker
  ```

---

### 3. macOS

For Apple systems using the Homebrew package manager, install Docker Desktop via an application cask:

* **Step 1: Install the complete Docker Desktop package**
  ```bash
  brew install --cask docker
  ```

> [!NOTE]
> After installation, launch Docker Desktop from your Applications folder. The Docker daemon will start automatically.

---

## ✅ Post-Installation Verification

After installing Docker on any platform, verify that the installation was successful:

```bash
# Check the Docker version
docker version

# Run the hello-world test container
docker run hello-world
```

> [!TIP]
> If `docker run hello-world` prints a success message, your Docker installation is working correctly!

---

<div align="center">

| 🏠 Home | Next ➡️ |
|:---:|:---:|
| [README](./README.md) | [Images & Run Basics](./Commands/01-Images-and-Run-Basics.md) |

</div>