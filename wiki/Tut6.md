> ## Documentation Index
> Fetch the complete documentation index at: https://ayakaleaf-pro.mintlify.site/llms.txt
> Use this file to discover all available pages before exploring further.

# 5. Personalizing your instance

> Set the port, site URL and TeX Live image, enable sandboxed compiles and start Ayakaleaf Pro on WSL 2.

Run commands and edit files in **Ubuntu**, from `~/ayakaleaf-toolkit`.

**1. Container settings**

Edit the existing entries in `config/overleaf.rc`:

```bash title="config/overleaf.rc" theme={null}
PROJECT_NAME=ayakaleaf-wsl
SERVER_PRO=true
OVERLEAF_LISTEN_IP=127.0.0.1
OVERLEAF_PORT=8080
SIBLING_CONTAINERS_ENABLED=true
DOCKER_SOCKET_PATH=/var/run/docker.sock
```

Keep the default data paths and bundled MongoDB and Redis services. Port `8080` is for the local Windows browser; binding to `127.0.0.1` keeps it local. Sandboxed compiles use a separate Linux container for each compile, rather than running TeX inside the application container. The Docker socket lets the application create those containers, and the Toolkit supplies the host path for the compile data under `data/overleaf/data/compiles`.

**2. Application settings**

In `config/variables.env`, set these values, replacing any active entries with the same names:

```dotenv title="config/variables.env" theme={null}
OVERLEAF_SITE_URL=http://localhost:8080
TEX_LIVE_DOCKER_IMAGE=ghcr.io/ayaka-notes/texlive-full:2026.1
ALL_TEX_LIVE_DOCKER_IMAGES=ghcr.io/ayaka-notes/texlive-full:2026.1
ALL_TEX_LIVE_DOCKER_IMAGE_NAMES="TeX Live 2026"
```

The full image includes the packages and fonts needed for a broad range of projects. Allow at least 40 GB of free storage for it and the application, plus space for project data and future updates.

For a quick deployment test, use the smaller **basic** image instead. It is enough for the plain article test in step 6, but does not include the full package and font set:

```dotenv theme={null}
TEX_LIVE_DOCKER_IMAGE=ghcr.io/ayaka-notes/texlive-basic:2026.1
ALL_TEX_LIVE_DOCKER_IMAGES=ghcr.io/ayaka-notes/texlive-basic:2026.1
ALL_TEX_LIVE_DOCKER_IMAGE_NAMES="TeX Live 2026 Basic"
```

Choose one of these sets, without duplicate active keys. The basic image's [Dockerfile](https://github.com/ayaka-notes/texlive-full/blob/main/texlive/2026/Dockerfile.basic) includes `latexmk`, XeTeX and `fontspec`, but not `ctex` or the full font collection. A missing LaTeX package in basic does not by itself indicate a sandbox problem.

Generate a session secret:

```bash theme={null}
openssl rand -hex 32
```

Add the result as `OVERLEAF_SESSION_SECRET` in `config/variables.env`. Keep the generated invite-token secret too. For a local instance without email, leave SMTP settings and `OVERLEAF_EMAIL_FROM_ADDRESS` unset.

**3. Start your instance**

```bash theme={null}
bin/up -d
```

The Toolkit starts MongoDB, initializes its replica set, downloads the selected TeX Live image, and starts Redis and the application. It may appear to pause while a large image downloads. Do not run a second `bin/up` while the first is still working.

In another Ubuntu terminal, check:

```bash theme={null}
cd "$HOME/ayakaleaf-toolkit"
bin/docker-compose ps
bin/docker-compose logs --tail=100 mongo
bin/docker-compose logs --tail=100 -f sharelatex
```

Press `Ctrl+C` to stop following logs; detached containers keep running. If startup fails at MongoDB, follow [MongoDB fails to start](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/windows/troubleshooting.md#mongodb-fails-to-start) before retrying. Do not delete its data directory to reset an existing installation.

Check the port and first-run page:

```bash theme={null}
bin/docker-compose port sharelatex 80
curl -I http://localhost:8080/launchpad
```

The port should be `127.0.0.1:8080`. Wait for application initialization if the HTTP request initially fails.

**4. Open the instance from Windows**

In your **Windows browser**, open [http://localhost:8080/launchpad](http://localhost:8080/launchpad). Use this address in Windows too, rather than a temporary WSL IP address.

To check Windows HTTP access separately from the browser, run in **PowerShell**:

```powershell theme={null}
curl.exe -I http://localhost:8080/launchpad
```

If Ubuntu can reach the page but Windows cannot, see [Windows cannot open localhost](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/windows/troubleshooting.md#windows-cannot-open-localhost).

### Next step

Continue with [6. Post-installation tasks](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/windows/6.-post-installation-tasks.md) to create the administrator and verify sandboxed compiles and persistence.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.