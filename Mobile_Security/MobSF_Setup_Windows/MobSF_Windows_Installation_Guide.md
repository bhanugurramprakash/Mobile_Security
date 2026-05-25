# MobSF Installation Guide on Windows
### Mobile Security Framework v4.5.0 — Complete Step-by-Step Guide

---

## What is MobSF?

Mobile Security Framework (MobSF) is an open-source, automated mobile application
security testing tool. It can perform static and dynamic analysis on Android (APK),
iOS (IPA), and Windows (APPX) apps. Security learners and pentesters use it to find
vulnerabilities in mobile apps without writing any code manually.

---

## Prerequisites Overview

| Tool | Version Used | Purpose |
|------|-------------|---------|
| Python | 3.13.9 | Core language MobSF runs on |
| Git | 2.52.0 | To clone MobSF source code from GitHub |
| Java JDK | 21 | Required for APK decompilation and analysis |
| Visual Studio Build Tools | 2022 v17.14.33 | Compiles Python C-extension packages |
| MobSF | v4.5.0 | The security framework itself |

---

## STEP 1 — Install Python 3.13

### Purpose
MobSF is built in Python. Python 3.12 or 3.13 is required by the latest MobSF version.
Without Python, MobSF cannot run at all.

### How to Install
1. Go to: https://www.python.org/downloads/
2. Click the big **"Download Python 3.x.x"** button
3. Run the downloaded `.exe` file
4. **CRITICAL** — Before clicking Install Now, check both boxes:
   - ☑ Use admin privileges when installing
   - ☑ **Add python.exe to PATH** ← most important
5. Click **"Install Now"**

### Verify Installation
Open Command Prompt and run:
```
python --version
```
Expected output:
```
Python 3.13.9
```

> **Why PATH matters:** Adding Python to PATH means you can run `python` from
> any folder in CMD. Without it, Windows cannot find the Python executable.

---

## STEP 2 — Install Git

### Purpose
Git is a version control tool. We use it to download (clone) the MobSF source
code directly from GitHub onto your machine with a single command.

### How to Install
1. Go to: https://git-scm.com/download/win
2. Download the 64-bit Windows installer
3. Run it with all default settings
4. Click Next through all options and Finish

### Verify Installation
```
git --version
```
Expected output:
```
git version 2.52.0.windows.1
```

---

## STEP 3 — Install Java JDK 21

### Purpose
MobSF uses Java to decompile APK files (convert compiled Android apps back into
readable code). Tools like JADX and Apktool that MobSF uses internally require Java.
Without Java, APK analysis will fail.

### Check if Already Installed
```
java -version
```
If you see `java version "21"` or higher, skip this step.

### How to Install (if not installed)
1. Go to: https://adoptium.net/
2. Download **Temurin JDK 21** — Windows x64 `.msi`
3. During install:
   - ☑ Set JAVA_HOME variable
   - ☑ Add to PATH
4. Click Next and Finish

### Verify Installation
```
java -version
```
Expected output:
```
java 21 2023-09-19 LTS
Java(TM) SE Runtime Environment (build 21+35-LTS-2513)
```

---

## STEP 4 — Install Visual Studio Build Tools 2022

### Purpose
Some Python packages that MobSF depends on (like `lief` for binary analysis)
are written in C/C++ and need to be compiled on your machine during installation.
Visual Studio Build Tools provides the Microsoft C++ compiler (MSVC) needed for this.
Without it, `pip install` will fail with "Microsoft Visual C++ 14.0 not found" errors.

### How to Install
1. Download from: https://aka.ms/vs/17/release/vs_BuildTools.exe
2. Run `vs_BuildTools.exe`
3. In the installer, check **"Desktop development with C++"** workload
4. Click **Install**
5. Wait 10–15 minutes (downloads ~4 GB)

### Verify Installation
After install, you will see:
```
Visual Studio Build Tools 2022
Version 17.14.33
All installations are up to date.
```

---

## STEP 5 — Disable Python App Execution Aliases

### Purpose
Windows has "App Execution Aliases" that redirect `python` and `python3` commands
to the Microsoft Store instead of your real Python installation. This confuses tools
like Poetry (MobSF's dependency manager) and causes it to pick the wrong Python version.
Disabling these aliases ensures MobSF always uses your real Python 3.13.

### How to Disable
1. Open Windows **Settings**
2. Go to **Apps → Advanced app settings → App execution aliases**
3. Find the following and toggle them **OFF**:
   - `python.exe` (App Installer) → **Off**
   - `python3.exe` (App Installer) → **Off**

---

## STEP 6 — Clone MobSF from GitHub

### Purpose
Cloning downloads the complete MobSF source code from GitHub to your local machine.
This gives you all the scripts, configuration files, and code needed to run MobSF.

### Commands
Open **Command Prompt** and run:
```
cd C:\Users\YourUsername
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
cd Mobile-Security-Framework-MobSF
```

### What You Will See
```
Cloning into 'Mobile-Security-Framework-MobSF'...
remote: Enumerating objects: 22251, done.
Receiving objects: 100% (22251/22251), 1.45 GiB | 8.67 MiB/s, done.
Updating files: 100% (530/530), done.
```

> The download is about 1.45 GB. This is normal — MobSF includes many analysis tools.

---

## STEP 7 — Run Setup

### Purpose
`setup.bat` is a Windows batch script provided by MobSF that:
- Checks your Python version
- Installs all required Python packages using Poetry
- Sets up the SQLite database
- Configures the MobSF environment
- Creates the default superuser account

### Command
Make sure you are inside the MobSF folder:
```
cd C:\Users\YourUsername\Mobile-Security-Framework-MobSF
setup.bat
```

### What You Will See When Done
```
[INSTALL] Migrating Database
[INSTALL] Installation Complete
```

> You may see yellow/red Poetry warnings about Python versions — these can be
> ignored as long as you see `[INSTALL] Installation Complete` at the end.

---

## STEP 8 — Launch MobSF

### Purpose
`run.bat` starts the MobSF web server. Once running, you can access the full
MobSF dashboard through your browser to upload and analyze APK files.

### Command
```
run.bat
```

### What You Will See
```
Mobile Security Framework v4.5.0
Author: Ajin Abraham | opensecurity.in
REST API Key: f932b6caec3cf639812b4ed443dab8cdc302cc257302d41551411f7bd3c5d97c
Default Credentials: mobsf/mobsf
Python Version: 3.13.9
Running MobSF on "0.0.0.0:8000 [::]:8000"
```

### Open in Browser
```
http://localhost:8000
```

---

## Login Credentials

| Field | Value |
|-------|-------|
| **URL** | http://localhost:8000 |
| **Username** | mobsf |
| **Password** | mobsf |

> These are the default credentials. You can change them in the MobSF settings
> after logging in for the first time.

---

## How to Stop MobSF

When you are done using MobSF, go to the CMD window where it is running and press:
```
Ctrl + C
```
This safely shuts down the MobSF server.

---

## How to Start MobSF Again (Every Time After First Setup)

You do NOT need to reinstall anything. Just run these two commands:

### Step 1 — Open CMD and navigate to MobSF folder
```
cd C:\Users\YourUsername\Mobile-Security-Framework-MobSF
```

### Step 2 — Start MobSF
```
run.bat
```

### Step 3 — Open browser
```
http://localhost:8000
```
Login with `mobsf` / `mobsf`

> Setup.bat only needs to be run once (first time). After that, always use run.bat.

---

## Create a Desktop Shortcut (Optional)

So you never have to type commands again:

1. Right-click Desktop → **New → Shortcut**
2. Paste this as the location (replace YourUsername):
```
cmd.exe /k "cd C:\Users\YourUsername\Mobile-Security-Framework-MobSF && run.bat"
```
3. Name it **"Start MobSF"**
4. Double-click it anytime to launch MobSF instantly

---

## Quick Summary — What to Do Every Session

```
1. Open CMD
2. cd C:\Users\YourUsername\Mobile-Security-Framework-MobSF
3. run.bat
4. Open browser → http://localhost:8000
5. Login: mobsf / mobsf
6. Upload APK and analyze
7. When done → Ctrl+C in CMD to stop
```

---

## Recommended APKs for Practice

| APK | Description | Best For |
|-----|-------------|----------|
| DIVA APK | Damn Insecure & Vulnerable App | Beginners — most popular |
| InsecureBankv2 | Simulated banking app | Banking app vulnerabilities |
| InjuredAndroid | CTF-style challenges | Intermediate learners |
| AndroGoat | OWASP Mobile Top 10 | Structured learning |

Download DIVA APK: https://github.com/payatu/diva-android

---

## Important Notes

- **Static Analysis** works immediately after setup — no emulator needed
- **Dynamic Analysis** requires Genymotion or Android Studio emulator to be running
- MobSF runs locally on your machine — your APKs are not uploaded to any server
- Default port is 8000 — if busy, run: `run.bat 8001`

---

## System Used for This Guide

| Component | Details |
|-----------|---------|
| OS | Windows 11 (10.0.26200) |
| Python | 3.13.9 |
| Git | 2.52.0 |
| Java | 21 LTS |
| Build Tools | Visual Studio 2022 v17.14.33 |
| MobSF | v4.5.0 |
| CPU | 4 Cores, 8 Threads |
| RAM | 7.65 GB |

---

*Guide prepared based on actual installation experience on Windows 11.*
*MobSF GitHub: https://github.com/MobSF/Mobile-Security-Framework-MobSF*
