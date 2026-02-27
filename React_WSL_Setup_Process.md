# React Native WSL2 & Android Studio Integration Guide

## Overview
This setup enables a high-performance React Native development environment where the code resides in **WSL2 (Ubuntu)** while the UI/hardware interaction happens on **Windows**.

---

## Step-by-Step Installation Guide

### Step 1: Install Prerequisites in WSL
Open your WSL terminal and install required packages:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y openjdk-17-jdk unzip wget curl
```

Verify Java installation:
```bash
java -version
```

### Step 2: Create Android SDK Directory
```bash
mkdir -p ~/Android/sdk/cmdline-tools
cd ~/Android/sdk/cmdline-tools
```

### Step 3: Download Android Command Line Tools
Download the latest Linux command line tools from the [Android Studio downloads page](https://developer.android.com/studio#command-tools):
```bash
wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip -O cmdline-tools.zip
```

### Step 4: Extract and Organize SDK Tools
```bash
unzip cmdline-tools.zip
mv cmdline-tools latest
rm cmdline-tools.zip
```

Your structure should now be: `~/Android/sdk/cmdline-tools/latest/bin/`

### Step 5: Configure Environment Variables
Add the following to your `~/.bashrc`:
```bash
echo '
# Android SDK Configuration
export ANDROID_HOME=$HOME/Android/sdk
export PATH=$ANDROID_HOME/platform-tools:$ANDROID_HOME/cmdline-tools/latest/bin:$PATH

# WSL-Windows ADB Bridge
export WSL_HOST_IP=$(ip route show default | awk '"'"'{print $3}'"'"')
export ADB_SERVER_SOCKET=tcp:$WSL_HOST_IP:5037
' >> ~/.bashrc

source ~/.bashrc
```

### Step 6: Install SDK Components
```bash
sdkmanager --update
sdkmanager "platform-tools" "platforms;android-34" "build-tools;34.0.0"
```

### Step 7: Accept All Licenses
```bash
yes | sdkmanager --licenses
```

### Step 8: Verify Installation
```bash
sdkmanager --list_installed
adb --version
```

### Step 9: Install Android Studio on Windows
1. Download [Android Studio](https://developer.android.com/studio) for Windows
2. Run the installer and complete setup
3. Open Android Studio → SDK Manager → Ensure platform-tools is installed
4. Set up an Android Virtual Device (AVD) or connect a physical device

### Step 10: Configure Windows Firewall & ADB Bridge
Open **PowerShell as Administrator** on Windows and run:
```powershell
# Create firewall rule
New-NetFirewallRule -DisplayName "WSL ADB" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 5037 -Profile Any

# Get your WSL gateway IP (run this in WSL first: ip route show default | awk '{print $3}')
# Replace 123.456.789.012 with your actual gateway IP
netsh interface portproxy add v4tov4 listenaddress=123.456.789.012 listenport=5037 connectaddress=127.0.0.1 connectport=5037
```

### Step 11: Start ADB Server on Windows
In Windows Command Prompt or PowerShell:
```powershell
adb kill-server
adb -a nodaemon server start
```

### Step 12: Test the Connection
Back in WSL, verify device connectivity:
```bash
adb devices
```

You should see your connected device or emulator listed.

---

## Maintenance & Troubleshooting
* **Permission Denied**: Run `chmod +x android/gradlew` in your project folder.
* **Connection Refused**: Your IP or the WSL gateway has changed. Re-run the PortProxy command with the new IP from `ip route show default`.
* **No Devices Found**: Ensure the Windows ADB listener (`-a nodaemon`) is running and your PortProxy is active.
* **Hot Reloading**: If Metro can't find the device, run `adb reverse tcp:8081 tcp:8081` in WSL.