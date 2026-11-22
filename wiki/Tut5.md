> ## Documentation Index
> Fetch the complete documentation index at: https://ayakaleaf-pro.mintlify.site/llms.txt
> Use this file to discover all available pages before exploring further.

# 4. Get Ayakaleaf Pro Images

> Switch the Toolkit in Ubuntu on WSL 2 to the public Ayakaleaf Pro image.

Ayakaleaf Pro is a free Docker image on [GitHub Container Registry (ghcr.io)](https://github.com/ayaka-notes/ayakaleaf-pro/pkgs/container/overleaf-pro/). You don't need an account, a login or a commercial Overleaf license to pull it.

Open `config/overleaf.rc` and set:

```bash title="config/overleaf.rc" theme={null}
SERVER_PRO=true
```

The next time you run `bin/up`, the Toolkit pulls `ghcr.io/ayaka-notes/overleaf-pro` in the version from `config/version`, instead of the official Overleaf Community Edition.

The image is built for `arm64` and `amd64`, and Docker picks the one that matches the Docker engine architecture.

### Next step

Continue with [5. Personalizing your instance](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/windows/5.-personalizing-your-instance.md), which also starts the instance.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.