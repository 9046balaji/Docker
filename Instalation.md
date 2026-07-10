## Installation of Docker (Windows)

On Windows, you can choose between the standard graphical interface with **Docker Desktop** or a lightweight, text-only engine running directly inside **Windows Subsystem for Linux (WSL 2)**.

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

### Option A: Docker Desktop (Recommended Graphic Utility)

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


*(Alternatively, you can manually pull down the `.exe` installer binary from the official Docker website).*
* **Step 3: Establish Distro Core Integration**
1. Boot up **Docker Desktop** from your Windows Start Menu.
2. Navigate to **Settings > General** and verify **"Use the WSL 2 based engine"** is checked.
3. Head down to **Settings > Resources > WSL Integration**, toggle the switch next to your target installed distribution (e.g., `Ubuntu`), and click **Apply & Restart**.



---

### Option B: Bare-Metal Docker Engine inside WSL 2 (No Desktop GUI)

If you want to save resources and run Docker natively inside your WSL 2 terminal environment without executing the heavy Windows desktop application overhead:

* **Step 1: Open your Linux distribution terminal interface and flush legacy packages**
```bash
sudo apt-get remove docker docker-engine docker.io containerd runc

```


* **Step 2: Pull down and execute the official Docker automation deployment script**
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

```


* **Step 3: Map your active user account profile directly into the local docker security group**
```bash
sudo usermod -aG docker $USER

```


*(Close out your current terminal shell or run `newgrp docker` to trigger permissions updates instantly).*
* **Step 4: Enable the background initialization daemon**
Ensure your Linux subsystem starts systemd automatically. Append the following lines directly inside your system configuration file (`/etc/wsl.conf`):
```ini
[boot]
systemd=true

```


Finally, drop back to Windows PowerShell and hard-reset the backend stack to finalize configurations:
```powershell
wsl --shutdown

```


### Docker Desktop Installation (Standard Windows GUI Method)

If you prefer the standard Windows way—where you just download a file, double-click it, and click through a visual installation wizard without typing any terminal commands—here is the exact step-by-step guide.

```text
 ___________________________________________________________________
|                     GRAPHICAL INSTALLATION FLOW                   |
|                                                                   |
|   [ Web Browser ]  --->  [ Double-Click ]  --->  [ Setup Wizard ] |
|   (Download .exe)        (Run Installer)         (Click "OK")     |
|                                                                |
|                                                                v  |
|   [ Docker Desktop App ] <---------------------- [ PC Restart ]   |
|   (Accept Terms & Use)                                            |
|___________________________________________________________________|

```

---

### Step 1: Download the Application Installer File

1. Open your regular web browser (Chrome, Edge, etc.) and go to the official website: **[docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/)**.
2. Click the big blue button labeled **"Download for Windows"**.
3. Wait for the installer file (named something like `Docker Desktop Installer.exe`) to finish downloading to your **Downloads** folder.

### Step 2: Run the Installer (Double-Click)

1. Open your Windows **File Explorer** and go to your **Downloads** folder.
2. Find the `Docker Desktop Installer` file.
3. **Double-click** it to start the installation.
4. If a Windows security window pops up asking *"Do you want to allow this app to make changes to your device?"*, simply click **Yes**.

### Step 3: Click Through the Configuration Screen

A small configuration window will appear with two checkboxes:

* **Use WSL 2 instead of Hyper-V (Recommended):** Make sure this is **checked** (it is usually checked by default). This ensures Docker runs fast and smoothly.
* **Add shortcut to desktop:** Leave this checked if you want a clickable icon on your desktop screen.
* Click the blue **OK** button at the bottom right.

### Step 4: Wait and Restart Your PC

1. The installer will now unpack all the files. You will see a progress bar. Let it load completely (it takes about 2–3 minutes).
2. Once it finishes successfully, you will see a button that says **"Close and restart"** or **"Close and log out"**.
3. Click that button. Your computer will restart to finalize the setup.

### Step 5: Launching and Using the App

1. Once your computer turns back on, go to your desktop screen and look for the whale icon named **Docker Desktop**.
2. **Double-click** the Docker Desktop icon to open it.
3. A **Service Agreement** window will pop up the very first time you open it. Click the **Accept** button.
4. On the next screen, you can choose **"Use recommended settings"** and click **Finish**.

> 🎉 **Success!** You will now see the main visual dashboard of Docker Desktop. You can completely manage your containers, images, and settings from this graphic dashboard screen just by clicking buttons, without ever needing to touch a command prompt!




## Installation of Docker

Here is the clean, formatted transcription of your installation notes across different operating systems:

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

Open your terminal and run the following sequential commands:

* **Step 1: Update the local package repository tracking list**
```bash
sudo apt update

```


* **Step 2: Download and install the core Docker runtime engine utilities**
```bash
sudo apt install docker.io -y

```


* **Step 3: Enable the system service and instantly initialize the Docker daemon background process**
```bash
sudo systemctl enable --now docker

```



---

### 2. CentOS / RHEL

For Red Hat enterprise ecosystems, use the standard package manager utilities:

* **Step 1: Download and install the Docker package automatically**
```bash
sudo yum install -y docker

```


* **Step 2: Start and configure the background Docker service to launch automatically on boot**
```bash
sudo systemctl enable --now docker

```



---

### 3. macOS

For Apple systems utilizing the Homebrew package manager, install Docker Desktop via the application application cask:

* **Step 1: Download and install the complete GUI and engine package**
```bash
brew install --cask docker

```