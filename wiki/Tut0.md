> ## Documentation Index
> Fetch the complete documentation index at: https://ayakaleaf-pro.mintlify.site/llms.txt
> Use this file to discover all available pages before exploring further.

# Introduction

> Deploy Ayakaleaf Pro with guidance on migration, requirements, and LaTeX performance.

Ayakaleaf Pro is a drop-in replacement for the official Overleaf Community Edition, shipped as a Docker image.

## Choose your installation path

> [card] [Install on Linux](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/linux)
>
> Using Docker Compose on Linux (with Docker)

> [card] [Install on Windows](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/windows)
>
> Using Docker Compose on Windows (with WSL)

> [card] [Install on Mac](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/macos)
>
> Using Docker Compose on macOS (Docker Desktop)

> [card] [Run it on a Kubernetes cluster](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-kubernetes)
>
> Using Kubernetes (experimental)

> [card] [Run it on a tiny server](https://ayakaleaf-pro.mintlify.site/on-premises/maintenance/small-server.md)
>
> For Small server only (needs 2 GB of swap)

> [card] [Let Claude or Codex do the install](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-claude-or-codex.md)
>
> Using Claude or Codex

> [card] [Try it quickly in the browser](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-the-github-codespace.md)
>
> Using GitHub Codespace (minimal TeX Live)

> [card] [Migrate an existing Overleaf](https://ayakaleaf-pro.mintlify.site/on-premises/installation/migrate-from-existing.md)
>
> Migrate from Existing Overleaf instance

> [card] [Install without internet access](https://ayakaleaf-pro.mintlify.site/on-premises/installation/air-gapped-offline-deployments.md)
>
> Air-gapped/offline deployments for internal usage

> [!NOTE]
>
> **Coming from the official Overleaf Server Pro?** ([What is the difference?](https://ayakaleaf-pro.mintlify.site/on-premises/readme/ayakaleaf-pro-vs-server-pro.md)) Open an issue before migrating: templates are stored differently, and we'll help you for free.\
> **Want to go back to the official Overleaf Community Edition?** Set `SERVER_PRO=false` (ayaka-notes Toolkit) or remove the override file (official Toolkit). No data is lost.

### Hardware requirements

We conducted relevant tests and found that, if you run Overleaf on a 2-core, 2 GB RAM server, its core components—primarily based on Node.js—will consume approximately 1 GB of memory. Redis and MongoDB may require an additional 0.5 GB, while starting a sandboxed Docker container for compilation can consume another 0.3 GB. This means that a 2-core, 2 GB server can realistically handle only about one concurrent compilation at a time.

* The minimum hardware requirements for installing Ayakaleaf Pro are a 2-core CPU and 3 GB of RAM.
* Due to the size of our TeX Live image, at least 40 GB of storage is required to enable sandboxed compilation.
* If you want to install the complete set of TeX Live images from 2020 through 2026, please make sure you have at least 150 GB of free disk space available.

Here are some recommended hardware configurations for different usage scenarios:

| Use Case | Recommended Configuration |
| - | - |
| Personal use, with occasional collaboration | 2 CPU cores, 4 GB RAM, 80 GB storage |
| Small teams, up to 10 users with light collaboration | 2 CPU cores, 8 GB RAM, 256 GB storage |
| Medium-sized teams, up to 100 users | 4 CPU cores, 16 GB RAM, 1 TB storage |
| Large-scale or university deployments | Distributed cluster deployment, with S3 storage; please contact us if you need deployment assistance |

### Software requirements

For software requirements, we recommend using a recent and stable version of Ubuntu (e.g. Ubuntu 24.04 in 2025–2026, Ubuntu 22.04 in 2023–2024) together with the latest stable version of Docker.

> [!NOTE]
>
> Ubuntu 26.04 ships with Linux kernel 7.0, and MongoDB refuses to start on Linux kernel 6.19 through 7.0.13 ([details](https://www.mongodb.com/docs/manual/administration/production-notes/)). To run on these kernels, add the following to the `mongo` service in `overleaf-toolkit/config/docker-compose.override.yml` (the same file that sets the Ayakaleaf Pro image). Our CI runs on Ubuntu 26.04 with this setting. Kernel 7.0.14 and later don't need it.
>
> ```yml title="docker-compose.override.yml" theme={null}
> services:
>     mongo:
>         environment:
>             GLIBC_TUNABLES: glibc.pthread.rseq=1
> ```

We recommend Linux for shared and production servers. You can also run Ayakaleaf Pro on [macOS](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/macos) or [Windows with WSL 2](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/windows) using Docker Desktop. Follow the guide for your platform. The images support both AMD64 and ARM64.

### LaTeX Performance

LaTeX is a single threaded program, meaning it can only utilize one CPU core at a time. When compiling a single document, performance is primarily limited by the CPU's single-core clock speed. Therefore the faster the single core performance of your CPU the faster you will be able to compile a document. More CPU cores are only beneficial when multiple users are compiling documents concurrently.

Using the [ThuThesis template](https://www.overleaf.com/latex/templates/thuthesis-tsinghua-university-thesis-latex-template/cfwgcxtvkbsx) as an example, the compilation time is about 6 seconds on an Intel Core i9-14900K (5.6 GHz), while it takes about 13 seconds on an Azure Standard D2as v5 instance (2 vCPUs, 8 GiB RAM) powered by an AMD EPYC 7763 (3.2 GHz).

Of course, actual compilation performance depends on your hardware configuration. Most server CPUs do not run at such high clock speeds as 14900K, so you will need to make a trade-off between cost and compilation speed.

Meanwhile, we have also conducted an in-depth analysis of LaTeX compilation performance. If you are interested, please refer to our blog [Overleaf Benchmark: A Deep Research of Concurrent LaTeX Compilation in Overleaf (SaaS and Self-host)](https://ayakaleaf-pro.mintlify.site/blog/2026/overleaf-benchmark.md "mention").


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.


Note: 
Github page of ayakaleaf-pro software: 
https://github.com/ayaka-notes/ayakaleaf-pro.git