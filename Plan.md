# Ayakaleaf Pro Local Installation Plan

## Overview
This plan outlines the structured milestones for installing Ayakaleaf Pro (a local, open alternative to Overleaf) on Windows 11. The installation uses WSL 2 + Docker Desktop, following the tutorial notes from the project wiki.

---

## Milestone 1: Windows Environment Preparation
**Goal:** Get WSL 2 and Docker Desktop running properly on Windows 11.

- [ ] **1.1 Install WSL 2 and Ubuntu**
  - Open PowerShell as Administrator
  - Run: `wsl --install -d Ubuntu`
  - Restart Windows if prompted
  - Open Ubuntu from Start menu, create Linux username/password
  - Update WSL: `wsl --update`
  - Verify version: `wsl --version` (must show VERSION 2)
  - If VERSION 1: `wsl --set-version Ubuntu 2`

- [ ] **1.2 Install and start Docker Desktop**
  - Download Docker Desktop for Windows (match processor architecture)
  - Run installer, select WSL 2 backend if offered
  - Open Docker Desktop from Start menu, wait for engine to start
  - Docker Desktop Settings → General: enable "Use WSL 2 based engine" if shown
  - Settings → Resources → WSL Integration: enable **Ubuntu**, then Apply/Apply & restart

- [ ] **1.3 Verify Docker from Ubuntu**
  - Open Ubuntu terminal
  - Run: `docker version` (must show Client and Server)
  - Run: `docker compose version`
  - Run: `docker ps` (empty list is normal)

- [ ] **1.4 Check resources and Docker socket**
  - Run: `docker info --format 'CPUs={{.NCPU}} Memory={{.MemTotal}} Kernel={{.KernelVersion}}'`
  - Run: `df -h "$HOME"`
  - Run: `ls -l /var/run/docker.sock`
  - **Suggestion for small instance:** 4 CPUs and 8 GB memory available to Docker
  - **Important:** Keep `DOCKER_SOCKET_PATH=/var/run/docker.sock` in Toolkit config
  - Do **not** change socket permissions to `777`; restore Docker Desktop integration instead

- [ ] **1.5 Next step:** Once `docker ps` works in Ubuntu, proceed to Milestone 2

---

## Milestone 2: Toolkit Download and Familiarization
**Goal:** Download the Ayakaleaf Toolkit and understand its structure.

- [ ] **2.1 Download the Toolkit**
  - In Ubuntu, follow the guide: "1. Download the Toolkit"
  - The toolkit scripts will be run from `~/ayakaleaf-toolkit`

- [ ] **2.2 Run doctor check**
  - From Ubuntu: `bin/doctor`
  - Review output for any warnings that need attention
  - Note: Warning about not being logged into `quay.io` does not apply to `ghcr.io/ayaka-notes/overleaf-pro`

- [ ] **2.3 Keep configuration with Linux line endings (LF)**
  - Edit config files with `nano` in Ubuntu or editor connected to WSL
  - CRLF line endings can cause Bash errors
  - Commands marked **PowerShell** run in Windows; `bin/` commands run in Ubuntu Bash

- [ ] **2.4 Next step:** Proceed to Milestone 3 once doctor passes

---

## Milestone 3: Image Configuration and Setup
**Goal:** Configure the Ayakaleaf Pro Docker image and settings.

- [ ] **3.1 Set SERVER_PRO=true**
  - Edit `config/overleaf.rc`:
    ```
    SERVER_PRO=true
    ```
  - This switches to the Ayakaleaf Pro image (ghcr.io/ayaka-notes/overleaf-pro) instead of official Overleaf CE

- [ ] **3.2 Configure container settings** (in `config/overleaf.rc`)
  ```bash
  PROJECT_NAME=ayakaleaf-wsl
  SERVER_PRO=true
  OVERLEAF_LISTEN_IP=127.0.0.1
  OVERLEAF_PORT=8080
  SIBLING_CONTAINERS_ENABLED=true
  DOCKER_SOCKET_PATH=/var/run/docker.sock
  ```
  - Keep default data paths and bundled MongoDB/Redis services
  - Port 8080 is for local Windows browser; binding to 127.0.0.1 keeps it local
  - Sandboxed compiles use separate Linux containers; Docker socket lets app create them

- [ ] **3.3 Configure application settings** (in `config/variables.env`)
  - Set port and URL:
    ```
    OVERLEAF_SITE_URL=http://localhost:8080
    ```
  - Set TeX Live Docker image (full recommended for broad package support):
    ```
    TEX_LIVE_DOCKER_IMAGE=ghcr.io/ayaka-notes/texlive-full:2026.1
    ALL_TEX_LIVE_DOCKER_IMAGES=ghcr.io/ayaka-notes/texlive-full:2026.1
    ALL_TEX_LIVE_DOCKER_IMAGE_NAMES="TeX Live 2026"
    ```
  - **Optional: basic image** (smaller, enough for simple tests):
    ```
    TEX_LIVE_DOCKER_IMAGE=ghcr.io/ayaka-notes/texlive-basic:2026.1
    ALL_TEX_LIVE_DOCKER_IMAGES=ghcr.io/ayaka-notes/texlive-basic:2026.1
    ALL_TEX_LIVE_DOCKER_IMAGE_NAMES="TeX Live 2026 Basic"
    ```
  - **40 GB minimum free storage** required for full image + application
  - Generate session secret:
    ```bash
    openssl rand -hex 32
    ```
  - Add result as `OVERLEAF_SESSION_SECRET` in `config/variables.env`
  - Keep generated invite-token secret
  - Leave SMTP settings and `OVERLEAF_EMAIL_FROM_ADDRESS` unset for local instance without email

- [ ] **3.4 Next step:** Proceed to Milestone 4

---

## Milestone 4: Initial Startup
**Goal:** Start the Ayakaleaf Pro instance and verify it runs.

- [ ] **4.1 Start the instance**
  - From Ubuntu, in `~/ayakaleaf-toolkit`:
    ```bash
    bin/up -d
    ```
  - **Important:** Do not run a second `bin/up` while the first is still working
  - The toolkit will: start MongoDB + init replica set, download selected TeX Live image, start Redis and the application
  - May appear to pause while large image downloads (allow sufficient time)

- [ ] **4.2 Verify containers are running**
  ```bash
  bin/docker-compose ps
  ```
  - Check all expected services (sharelatex, mongo, redis) are up

- [ ] **4.3 Check startup logs**
  ```bash
  bin/docker-compose logs --tail=100 mongo
  bin/docker-compose logs --tail=100 -f sharelatex
  ```
  - Press `Ctrl+C` to stop following logs; containers remain running detached
  - If MongoDB fails to start, consult troubleshooting guide before retrying
  - Do **not** delete MongoDB data directory to reset existing installation

- [ ] **4.4 Check port and first page**
  ```bash
  bin/docker-compose port sharelatex 80
  curl -I http://localhost:8080/launchpad
  ```
  - Port should show as `127.0.0.1:8080`
  - Wait for application initialization if HTTP request initially fails

- [ ] **4.5 Open from Windows browser**
  - Open: `http://localhost:8080/launchpad`
  - **Use WSL IP address only as fallback**; prefer `localhost:8080` from Windows

- [ ] **4.6 Next step:** Proceed to Milestone 5

---

## Milestone 5: Post-Installation and Verification
**Goal:** Create administrator, verify compiles, ensure persistence.

- [ ] **5.1 Create administrator and first project**
  - In Windows browser, open: `http://localhost:8080/launchpad`
  - Create the first administrator account and log in

- [ ] **5.2 Verify sandboxed compilation**
  - Create a blank project and compile with **pdfLaTeX**:
    ```latex
    \documentclass{article}
    \begin{document}
    Ayakaleaf Pro on Windows WSL 2.
    \end{document}
    ```
  - PDF preview should show the text
  - **XeLaTeX** test (requires full image):
    ```latex
    \documentclass{article}
    \usepackage{fontspec}
    \begin{document}
    Ayakaleaf WSL XeLaTeX sandbox.
    \end{document}
    ```
  - **Chinese document test** (requires ctex-supported image):
    ```latex
    \documentclass{ctexart}
    \begin{document}
    你好，Ayakaleaf Pro。
    \end{document}
    ```
  - **Confirm sandbox use** (in Ubuntu while compiling):
    ```bash
    docker ps -a --filter ancestor=ghcr.io/ayaka-notes/texlive-basic:2026.1
    bin/logs -f clsi
    ```
    - A compiler container should use the expected image and `/compile` bind mount

- [ ] **5.3 Verify persistence**
  - From Ubuntu Toolkit directory:
    ```bash
    bin/stop
    bin/start
    ```
  - Wait for application, reopen project from Windows and compile again
  - Account, source, and project files should still be there
  - **Default live data location:** `~/ayakaleaf-toolkit/data/` inside Ubuntu
  - **Important:** Keep both `config/` and `data/` directories; uninstalling/unregistering Ubuntu can delete them

- [ ] **5.4 Keep instance available long-term**
  - **Docker Desktop:** Enable "Start Docker Desktop when you sign in to your computer"
  - **WSL settings:** For AC power users, set Windows power settings:
    - Settings → System → Power & battery → Screen, sleep & hibernate timeouts
    - Set plugged-in sleep timeout to **Never**
    - Disable automatic hibernation if configured
    - Display can still turn off
  - **Lid action:** Separate setting; closing lid can sleep even with Never timeout
  - **Toolkit restart policy:** Services use `restart: always`
    ```bash
    docker inspect sharelatex mongo redis --format '{{.Name}} restart={{.HostConfig.RestartPolicy.Name}}'
    ```
    - Each should report `always`
  - **Do not** run `wsl --shutdown` as routine cleanup while site is in use
  - **Do not** manually Pause Docker Desktop while serving users

- [ ] **5.5 Daily use reference**
  - Start Docker Desktop in Windows
  - Open Ubuntu: `cd "$HOME/ayakaleaf-toolkit"`
  - Common commands:
    | Task | Command |
    |------|---------|
    | Start existing containers | `bin/start` |
    | Stop containers and keep data | `bin/stop` |
    | Apply configuration changes | `bin/up -d` |
    | Check status | `bin/docker-compose ps` |
    | Read startup logs | `bin/docker-compose logs --tail=100 sharelatex` |
    | Read MongoDB logs | `bin/docker-compose logs --tail=100 mongo` |
    | Read compiler logs | `bin/logs -f clsi` |

- [ ] **5.6 Check recovery after restarting Docker Desktop**
  - Save work, allow active compiles to finish
  - Docker Desktop → Troubleshooting → Restart Docker Desktop
  - Wait for engine, then in Ubuntu:
    ```bash
    docker ps
    curl -I http://localhost:8080/launchpad
    ```
  - Open existing project in Windows and compile again
  - Confirm account and source still exist
  - If stopped with `bin/start`, use `bin/start` to resume
  - If service fails, read logs rather than recreating configuration

- [ ] **5.7 Installation complete** – Ayakaleaf Pro is running locally as a 100% local Overleaf alternative