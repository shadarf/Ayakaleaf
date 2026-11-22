> ## Documentation Index
> Fetch the complete documentation index at: https://ayakaleaf-pro.mintlify.site/llms.txt
> Use this file to discover all available pages before exploring further.

# 2. Familiarize yourself with the Toolkit

> What differs on Windows when you run the Toolkit scripts in WSL 2.

The Toolkit is the same as on Linux. Its layout, the `config/` directory and the `bin/` commands are described in [2. Familiarize yourself with the Toolkit](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/linux/2.-familiarize-yourself-with-the-toolkit.md) of the Linux guide.

On Windows, keep these differences in mind:

* Run `bin/` commands in **Ubuntu Bash**, from `~/ayakaleaf-toolkit`. Commands marked **PowerShell** run in Windows instead.
* Start Docker Desktop and enable Ubuntu's WSL Integration before using `bin/up`, `bin/start` or `bin/docker-compose`.
* Keep configuration files with Linux line endings (LF). Edit them with `nano` in Ubuntu or an editor connected to WSL. CRLF line endings can cause Bash errors.
* `bin/doctor` runs Linux checks inside Ubuntu. Its warning about not being logged in to `quay.io` does not apply to the public `ghcr.io/ayaka-notes/overleaf-pro` image. Other warnings still need attention.

```bash theme={null}
bin/doctor
```

### Next step

Continue with [3. Initialize the configuration](https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/windows/3.-initialize-the-configuration.md).


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.