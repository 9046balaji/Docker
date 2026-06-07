### Multi-Stage Builds

(1) We use a **single Dockerfile**, but we write **multiple stages** within it to optimize the final output.

```text
 .-------------------------------------------------------------.
|                          DOCKERFILE                          |
|  ===========================================================  |
|   STAGE 1: BUILD ENVIRONMENT (e.g., FROM ubuntu/golang)      |
|   - Installs heavy compilers, SDKs, and build tools          |
|   - Downloads bulky dependencies & source code               |
|   - Compiles application into a production-ready binary      |
|                               |                              |
|                               | (Copies ONLY the compiled)   |
|                               v (executable binary artifact) |
|   STAGE 2: RUNTIME ENVIRONMENT (e.g., FROM alpine/scratch)   |
|   - Minimalistic, ultra-lightweight base image               |
|   - Contains no compilers or caching junk                    |
|   - CMD [ "./app" ]                                          |
|______________________________________________________________|
                                |
                                v
                       [ FINAL SLIM IMAGE ]
             (Tiny file size, secure, zero bloated tools)

```

Multi-stage builds let you use multiple `FROM` instructions in a single Dockerfile. This allows you to completely separate your **build-time** concerns from your **run-time** concerns, keeping the final production image incredibly small and secure.

---

### (1) The Core Idea

In a **normal (single-stage) Dockerfile**, every single step you execute (installing heavy build tools, downloading dependencies, compiling the code, running the app) ends up baked into the final image. This causes severe issues:

* **Larger Images:** All the unnecessary compilers, source codes, and local package caches stay trapped inside the final image.
* **Less Secure:** More installed software means a much larger attack surface for malicious vulnerabilities.

#### How Multi-Stage Builds Solve This:

* **Multiple Stages:** You can declare multiple `FROM` lines. Each `FROM` starts a brand-new, clean stage with a completely different base image.
* **Artifact Copying:** You build your application in the early stages, then copy *only* the finalized compiled artifacts (e.g., a single executable binary or optimized static web assets) into the final minimal stage.
* **Dropping Bloat:** All the heavy compilers, tracking tools, and temporary files are automatically dropped and left behind in the previous stages—never making it into your production registry.

---

### * How Many Stages Can We Use?

The number of stages you can use is **countless**. However, no matter how many temporary building stages you create, there will always be **only one final stage** which outputs the ultimate minimalistic production image.

### Multi-Stage Docker Working

In a multi-stage Dockerfile, developers define **multiple build stages**, each encapsulating a specific set of instructions and dependencies. These stages can be uniquely named and referenced within the same Dockerfile, enabling seamless communication and artifact transfer between them.

```text
 _______________________________________________________________
|  STAGE 1: BUILD STAGE (e.g., FROM golang:1.22)               |
|  - Contains full SDK, Compilers, Package Managers, Caches    |
|  - Compiles Source Code -> [ App Binary ]                   |
|_______________________________________________________________|
                                |
                                | (Copy binary artifact ONLY)
                                v
 _______________________________________________________________
|  STAGE 2: FINAL RUNTIME IMAGE (e.g., FROM distroless/alpine)  |
|  - NO Compilers, NO Caches, NO Source Code                    |
|  - ONLY minimal runtime dependencies + [ App Binary ]        |
|_______________________________________________________________|
                                |
                (Intermediate stages are DISCARDED)
                                v
                  [ Production-Ready Image ]
                    (Size reduced by 90%+)

```

* **The Build Stage (First Stage):** This stage is fully dedicated to building the application code. It utilizes a full Software Development Kit (SDK) containing all the heavy tools needed to compile software. It downloads dependencies, processes raw source code, and builds the standalone binary or executable.
* **The Runtime Stage (Subsequent Stages):** These stages focus purely on packaging the finalized application and preparing it for execution. It creates a lightweight runtime image that contains **zero source code** and **no compilers**.
* **Automatic Cleanup:** All intermediate images and layers generated in the earlier build stages are automatically **discarded** right after their purpose is served.

> **Final Product:** You are left with a single production image that contains strictly the essential components required to run your app, often reducing the overall image size by **90% or more**.

---

### Distroless Images in Multi-Stage Dockerfiles

Distroless images represent the absolute minimum when it comes to lightweight and secure Docker environments. They are minimalistic images that contain **only** your application and its runtime dependencies.

```text
  STANDARD LINUX IMAGE (Ubuntu/Debian)      DISTROLESS RUNTIME IMAGE
 .------------------------------------.    .--------------------------.
|  [Shell] [Package Manager] [Utils]  |   |                          |
|  [ls] [curl] [find] [bash] [apt]    |   |    NO SHELL / NO UTILS   |
|  ---------------------------------  |   |  ----------------------  |
|  [ Application Runtime Dependencies ]|   |  [ Application Runtime ] |
|  [ Final Executable App Binary     ]|   |  [ App Binary Executable]|
 '------------------------------------'    '--------------------------'

```

* **Pure Runtime Environment:** Unlike traditional base images (like Ubuntu or Debian), distroless images do not contain package managers, shells, or any of the standard system utilities.
* **No Basic Shell Commands:** Because they are stripped down for security, there is **no system shell** (`/bin/sh` or `/bin/bash`). Basic commands like `ls`, `find`, or `curl` simply do not exist inside a distroless container.
* **Singular Focus:** Their sole purpose is to safely isolate and execute your application process. You directly trigger the entrypoint via instructions like: `CMD ["/app"]`. This creates a near-impenetrable layer because a hacker cannot execute malicious shell scripts if there is no shell available to run them.

### Google Distroless Images & Multi-Stage Integration

Distroless images are ultra-lightweight container images provided by Google. They contain **only** your specific application and its direct runtime dependencies. They entirely strip away package managers, system shells, or standard upstream Linux operating system utilities. This makes them drastically smaller and fundamentally more secure than traditional base environments like Ubuntu or Debian.

```text
 _______________________________________________________
|  BUILD STAGE (Heavier Base Image, e.g., Ubuntu/Go)    |
|  - Includes OS Utilities, Compilers, Package Managers |
|  - Compiles App -> Creates Executable Artifacts       |
|_______________________________________________________|
                           |
                           | (Copies ONLY finalized artifacts)
                           v
 _______________________________________________________
|  FINAL RUNTIME STAGE (Google Distroless Image)        |
|  - NO Shell (/bin/bash, /bin/sh)                      |
|  - NO Package Manager (apt, apk)                      |
|  - NO Linux Utilities (ls, curl, find)                |
|  - ONLY Application Executable + Pure Runtime Stack   |
|_______________________________________________________|

```

---

### Core Advantages

#### (1) Maximum Security

Previously, development pipelines commonly relied on full-fledged operating system images (like Ubuntu) even inside the final production stage. This unintentionally exposed production environments to severe OS-level vulnerabilities that hackers could exploit. Moving to a distroless architecture completely removes this risk.

* **Real-World Example:** Consider a Python web application. If you migrate it to a Python-specific distroless runtime image, the container will *only* possess the core Python interpreter.
* Because it lacks basic system packages like `ls`, `find`, or `curl`, an attacker who manages to exploit an application vulnerability is instantly trapped—they cannot execute shell scripts, download malicious payloads, or map out your internal container filesystem. This drastically minimizes your overall attack surface.

#### (2) High Efficiency

By leaving out standard OS bloat, you get much smaller file sizes, faster deployment speeds, and a highly optimized production pipeline.

---

### Implementing Multi-Stage Docker with Distroless

To effectively use distroless images, you *must* pair them with a multi-stage Dockerfile workflow. Because distroless environments lack build tools and compilers, you cannot compile code directly inside them.

* **The Strategy:** Developers use a full, feature-rich development environment image in the initial build stages to compile code, run package managers, and assemble dependencies.
* **The Hand-off:** Once compilation finishes, you swap to a pristine Google distroless image for the final stage and copy **only** the compiled binaries or finalized runtime artifacts across the boundary. Heavy, bloated images are strictly restricted to the building phases, ensuring your final live container stays incredibly lean.
