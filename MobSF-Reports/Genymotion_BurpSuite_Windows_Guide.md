# Genymotion + Burp Suite Setup & Connection Guide on Windows
### Complete Step-by-Step Guide for Android Mobile Security Testing

---

## Overview

This guide covers:
1. Installing Genymotion on Windows
2. Installing Burp Suite on Windows
3. Creating an Android Virtual Device in Genymotion
4. Connecting Burp Suite proxy to Genymotion
5. Installing Burp CA Certificate on Android
6. Intercepting Android app traffic
7. Common issues and fixes

---

## System Requirements

| Component | Minimum |
|-----------|---------|
| OS | Windows 10 / 11 (64-bit) |
| RAM | 8 GB (16 GB recommended) |
| Storage | 20 GB free space |
| CPU | Intel VT-x or AMD-V virtualization enabled |
| Java | JDK 11+ (already installed) |

---

## PART 1 — Install Genymotion

### What is Genymotion?
Genymotion is an Android emulator used for mobile app testing.
It is faster than the default Android Studio emulator and
works well with Burp Suite for traffic interception.

### Step 1 — Download Genymotion

1. Go to: https://www.genymotion.com/product-desktop/
2. Click **"Download for Windows"**
3. Choose **"Genymotion for Personal Use (free)"**
4. Create a free account if asked

### Step 2 — Install VirtualBox (Required)

Genymotion runs on top of VirtualBox. If not already installed:

1. Go to: https://www.virtualbox.org/wiki/Downloads
2. Download **"Windows hosts"** installer
3. Run and install with all default settings
4. Restart your PC after VirtualBox install

### Step 3 — Install Genymotion

1. Run the downloaded Genymotion installer
2. During setup it will detect VirtualBox automatically
3. Install with all default settings
4. Launch Genymotion after install

### Step 4 — Create an Android Virtual Device

1. Open Genymotion
2. Click **"+"** (Add device)
3. Sign in with your Genymotion account
4. Search for: `Google Pixel 4`
5. Select **Android 11.0** version
6. Click **"Next"** → **"Install"**
7. Wait for download to complete (~2 GB)
8. Click **"Finish"**

### Step 5 — Start the Virtual Device

1. Select your Pixel 4 device
2. Click **"Start"**
3. Wait for Android to fully boot (takes 1-2 minutes)
4. You should see the Android home screen

### Verify ADB Connection

Open CMD and run:
```
adb devices
```
Expected output:
```
List of devices attached
192.168.x.x:5555    device
```

> If adb is not found, add Android SDK to PATH or use Genymotion's built-in ADB.

---

## PART 2 — Install Burp Suite

### What is Burp Suite?
Burp Suite is a web/mobile application security testing tool.
We use it as a proxy to intercept and analyse all network
traffic between the Android app and the internet.

### Step 1 — Download Burp Suite Community Edition

1. Go to: https://portswigger.net/burp/communitydownload
2. Click **"Download Burp Suite Community Edition"**
3. Select **Windows (64-bit)** installer
4. Run the downloaded `.exe`
5. Install with all default settings

### Step 2 — Launch Burp Suite

1. Open Burp Suite from Start menu
2. Select **"Temporary project"**
3. Click **"Next"** → **"Start Burp"**
4. Burp Suite dashboard opens

### Step 3 — Configure Burp Proxy Listener

1. Go to **Proxy** tab → **Proxy settings**
2. Under **Proxy listeners**, click **"Add"**
3. Set:
   - Bind to port: `8080`
   - Bind to address: **All interfaces**
4. Click **OK**
5. Make sure the listener shows **Running** status

---

## PART 3 — Connect Burp Suite to Genymotion

### Step 1 — Find Your Windows IP Address

Open CMD and run:
```
ipconfig
```
Look for **IPv4 Address** under your active network adapter:
```
IPv4 Address: 192.168.1.x
```
Note this IP — you will need it in the next step.

### Step 2 — Configure Proxy on Android (Genymotion)

Inside your running Genymotion Android device:

1. Go to **Settings** → **Wi-Fi**
2. Long press on the connected Wi-Fi network
3. Select **"Modify network"**
4. Tap **"Advanced options"**
5. Set **Proxy** to **Manual**
6. Enter:
   - Proxy hostname: `192.168.1.x` (your Windows IP from Step 1)
   - Proxy port: `8080`
7. Tap **Save**

### Step 3 — Verify Connection

1. Open any browser inside Genymotion Android
2. Go to: `http://burp`
3. You should see the **Burp Suite** page
4. In Burp Suite you should see traffic appearing in **Proxy → Intercept**

---

## PART 4 — Install Burp CA Certificate on Android

### Why This is Needed
Without the CA certificate, Android will block HTTPS traffic
and show SSL errors. Installing the certificate lets Burp
intercept encrypted (HTTPS) traffic.

### Step 1 — Download Burp CA Certificate

1. Open browser inside Genymotion
2. Go to: `http://burp`
3. Click **"CA Certificate"** in top right
4. The file `cacert.der` downloads to Android

### Step 2 — Convert Certificate Format

On your Windows machine:

1. Open CMD and run:
```
openssl x509 -inform DER -in cacert.der -out cacert.pem
openssl x509 -inform PEM -subject_hash_old -in cacert.pem | head -1
```
2. Note the hash number output (e.g. `9a5ba575`)
3. Rename the file:
```
copy cacert.pem 9a5ba575.0
```

### Step 3 — Push Certificate to Android

```
adb root
adb remount
adb push 9a5ba575.0 /system/etc/security/cacerts/
adb shell chmod 644 /system/etc/security/cacerts/9a5ba575.0
adb reboot
```

### Step 4 — Verify Certificate Installed

1. After reboot, go to Android **Settings**
2. Go to **Security** → **Trusted credentials**
3. Under **System** tab, look for **PortSwigger**
4. It should show as trusted ✅

---

## PART 5 — Connect MobSF Dynamic Analysis to Genymotion

### Step 1 — Start MobSF

Open CMD:
```
cd C:\Users\gurra\Mobile-Security-Framework-MobSF
run.bat
```

### Step 2 — Configure MobSF for Dynamic Analysis

1. Open browser → `http://localhost:8000`
2. Login: `mobsf` / `mobsf`
3. Go to **Dynamic Analyzer**
4. MobSF will auto-detect Genymotion if ADB is connected

### Step 3 — Verify ADB Connection to MobSF

In CMD:
```
adb connect 192.168.x.x:5555
```
Then check MobSF Dynamic Analyzer page — device should appear.

---

## PART 6 — Intercept Traffic (Full Workflow)

Once everything is connected, follow this workflow:

```
1. Start Genymotion → Boot Android device
2. Set Wi-Fi proxy → Your IP:8080
3. Start Burp Suite → Listener on 0.0.0.0:8080
4. Open app in Genymotion
5. Turn ON Intercept in Burp (Proxy → Intercept → On)
6. Use the app → requests appear in Burp
7. Analyse, modify, forward requests as needed
```

---

## COMMON ISSUES AND FIXES

### Issue 1 — ADB device not detected
```
error: no devices/emulators found
```
**Fix:**
```
adb kill-server
adb start-server
adb connect 192.168.x.x:5555
```

---

### Issue 2 — No traffic appearing in Burp
**Cause:** Wrong IP or proxy not set correctly on Android.

**Fix:**
- Re-check your Windows IP with `ipconfig`
- Make sure proxy is set on the correct Wi-Fi in Genymotion
- Disable Windows Firewall temporarily to test:
  `Windows Security → Firewall → Turn off`

---

### Issue 3 — SSL Error / Certificate Error in Android browser
**Cause:** Burp CA certificate not installed.

**Fix:**
- Repeat Part 4 steps carefully
- Make sure you are using `adb root` before pushing certificate
- Try using Android 7 or below device (easier certificate install)

---

### Issue 4 — Genymotion not starting / Black screen
**Cause:** Virtualization not enabled in BIOS.

**Fix:**
1. Restart PC → Enter BIOS (press F2/F10/Del on startup)
2. Find **Intel VT-x** or **AMD-V** option
3. Enable it → Save → Restart

---

### Issue 5 — Port 8080 already in use
```
Cannot listen on port 8080
```
**Fix:** Change Burp listener to port `8081` and update Android proxy port to `8081` too.

---

### Issue 6 — MobSF Dynamic Analyzer shows no device
**Fix:**
```
adb kill-server
adb start-server
adb devices
```
Then refresh MobSF Dynamic Analyzer page.

---

### Issue 7 — Apps showing certificate pinning errors
**Cause:** App uses SSL pinning and blocks Burp certificate.

**Fix:** Use Frida to bypass SSL pinning:
```
frida -U -f com.target.app -l ssl_bypass.js
```
SSL bypass scripts available at:
https://github.com/httptoolkit/frida-android-unpinning

---

## Quick Reference — Ports & IPs

| Service | Host | Port |
|---------|------|------|
| Burp Suite Proxy | 0.0.0.0 | 8080 |
| MobSF Web UI | localhost | 8000 |
| Genymotion ADB | 192.168.x.x | 5555 |
| Android Proxy | Your Windows IP | 8080 |

---

## Full Setup Checklist

```
[ ] VirtualBox installed
[ ] Genymotion installed and account created
[ ] Android Virtual Device created (Pixel 4, Android 11)
[ ] Device boots successfully
[ ] ADB detects device (adb devices)
[ ] Burp Suite installed
[ ] Burp listener running on 0.0.0.0:8080
[ ] Android Wi-Fi proxy set to Windows IP:8080
[ ] Browser in Android reaches http://burp
[ ] Burp CA certificate downloaded and installed
[ ] HTTPS traffic visible in Burp Proxy tab
[ ] MobSF Dynamic Analyzer detects device
[ ] Ready for dynamic analysis
```

---

## System Used for This Guide

| Component | Details |
|-----------|---------|
| OS | Windows 11 (10.0.26200) |
| Python | 3.13.9 |
| MobSF | v4.5.0 |
| Burp Suite | Community Edition |
| Genymotion | Personal Use |
| Android | Pixel 4 — Android 11 |

---

*Guide prepared based on hands-on setup experience on Windows 11.*  
*MobSF GitHub: https://github.com/MobSF/Mobile-Security-Framework-MobSF*  
*Burp Suite: https://portswigger.net/burp*  
*Genymotion: https://www.genymotion.com*
