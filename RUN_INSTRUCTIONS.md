# Ayakaleaf Pro - Windows User Guide

This guide explains how to start, stop, and use Ayakaleaf Pro on **Windows 11** using WSL 2 + Docker Desktop.

---

## Prerequisites (Already Done)

- ✅ WSL 2 with Ubuntu installed
- ✅ Docker Desktop running with **WSL Integration enabled for Ubuntu**
- ✅ Ayakaleaf Toolkit configured at `/home/shadm/ayakaleaf-toolkit` (inside Ubuntu)
- ✅ Containers built and ready

---

## Daily Use - Step by Step

### 1. Start Ayakaleaf Pro

**Option A: Quick Start (Recommended)**
```powershell
# Open PowerShell or Command Prompt
wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/start"
```

**Option B: Full Startup (if containers were stopped)**
```powershell
wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/up -d"
```

**What happens:**
- Starts MongoDB, Redis, and Ayakaleaf Pro containers
- Takes ~30-60 seconds for all services to be ready
- Access at: **http://localhost:8080/launchpad**

---

### 2. Open in Your Browser

1. Open **any Windows browser** (Chrome, Edge, Firefox)
2. Go to: **http://localhost:8080/launchpad**
3. **First time only:** Create your administrator account
4. **Subsequent visits:** Log in with your credentials

---

### 3. Create a Test Project (Verify It Works)

1. Click **"New Project"** → **"Blank Project"**
2. Name it (e.g., "Test")
3. Open `main.tex` and replace with:

   **Simple test (pdfLaTeX):**
   ```latex
   \documentclass{article}
   \begin{document}
   Ayakaleaf Pro on Windows WSL 2.
   \end{document}
   ```

4. Click **"Recompile"** (or press `Ctrl+Enter`)
5. PDF preview should appear on the right

---

### 4. Stop Ayakaleaf Pro (Keep Your Data)

```powershell
wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/stop"
```

- Stops all containers
- **Your projects, accounts, and data are preserved**
- Use this when you're done for the day

---

### 5. Restart Later

```powershell
wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/start"
```

- Restarts containers with all your data intact
- Ready in ~10-15 seconds

---

## Quick Reference Card

| Task | Command (Run in PowerShell/CMD) |
|------|----------------------------------|
| **Start** (if stopped) | `wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/start"` |
| **Start** (first time / after config change) | `wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/up -d"` |
| **Stop** (keep data) | `wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/stop"` |
| **Check status** | `wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/docker-compose ps"` |
| **View logs** | `wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/docker-compose logs --tail=100 sharelatex"` |
| **Open launchpad** | Browser → `http://localhost:8080/launchpad` |

---

## PowerShell Shortcuts (Optional)

Add these to your PowerShell profile for easier access:

```powershell
# Open PowerShell profile
notepad $PROFILE

# Add these functions:
function Ayaka-Start {
    wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/start"
    Write-Host "Opening http://localhost:8080/launchpad ..."
    Start-Process "http://localhost:8080/launchpad"
}

function Ayaka-Stop {
    wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/stop"
}

function Ayaka-Status {
    wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/docker-compose ps"
}

function Ayaka-Restart {
    Ayaka-Stop
    Start-Sleep 3
    Ayaka-Start
}

# Save, then restart PowerShell
# Now you can just type: Ayaka-Start, Ayaka-Stop, Ayaka-Status, Ayaka-Restart
```

---

## Troubleshooting

### "localhost refused to connect"
1. Wait 30-60 seconds after running `bin/start` or `bin/up -d`
2. Check containers are running: `Ayaka-Status` (or the status command above)
3. All three should show "Up": `mongo`, `redis`, `sharelatex`
4. Try: `curl.exe -I http://localhost:8080/launchpad` - should return `HTTP/1.1 200 OK`

### Docker Desktop Issues
- Ensure **Docker Desktop is running** (whale icon in system tray)
- Settings → **General** → ✅ "Start Docker Desktop when you sign in"
- Settings → **Resources → WSL Integration** → ✅ **Ubuntu** enabled → **Apply & Restart**

### WSL Issues
```powershell
# Restart WSL completely
wsl --shutdown
# Then restart Docker Desktop, then run Ayaka-Start
```

### Reset Everything (Nuclear Option)
```powershell
# ⚠️ DELETES ALL YOUR PROJECTS AND DATA
wsl -d Ubuntu -- bash -c "cd /home/shadm/ayakaleaf-toolkit && bash bin/stop"
wsl -d Ubuntu -- bash -c "rm -rf /home/shadm/ayakaleaf-toolkit/data"
# Then run Ayaka-Start to reinitialize
```

---

## File Locations

| What | Where (Inside Ubuntu) |
|------|----------------------|
| Toolkit scripts | `/home/shadm/ayakaleaf-toolkit/bin/` |
| Configuration | `/home/shadm/ayakaleaf-toolkit/config/` |
| **Your projects & data** | `/home/shadm/ayakaleaf-toolkit/data/overleaf/` |
| MongoDB data | `/home/shadm/ayakaleaf-toolkit/data/mongo/` |
| Redis data | `/home/shadm/ayakaleaf-toolkit/data/redis/` |

**To backup:** Copy the entire `/home/shadm/ayakaleaf-toolkit/data/` folder from Ubuntu.

---

## Windows-Specific Notes

- **Use `http://localhost:8080/launchpad`** (not WSL IP addresses)
- **PowerShell `curl.exe`** works for testing: `curl.exe -I http://localhost:8080/launchpad`
- **File editing:** Use Windows editors on files in `\\wsl$\Ubuntu\home\shadm\ayakaleaf-toolkit\config\`
- **Line endings:** Config files must use **LF** (Linux style) - editors like VS Code handle this automatically

---

## Support

- **Documentation:** https://ayakaleaf-pro.mintlify.site/
- **GitHub:** https://github.com/ayaka-notes/ayakaleaf-pro
- **Troubleshooting:** https://ayakaleaf-pro.mintlify.site/on-premises/installation/using-docker-compose/windows/troubleshooting.md