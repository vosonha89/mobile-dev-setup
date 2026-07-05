# mobile-dev-setup

A comprehensive guide for setting up local development environment for mobile development, including Android SDK and iOS SDK configurations.

## Table of Contents

- [Setup Android SDK for Local Development](#1-setup-android-sdk-for-local-development)
- [Setup iOS SDK for Local Development](#2-setup-ios-sdk-for-local-development)

---

## 1. Setup Android SDK for Local Development

This guide covers Android SDK setup for **Linux 🐧**, **Windows 🪟**, and **macOS 🍎**.

📖 **References**:
- [Android SDK Manager (Official)](https://developer.android.com/tools/sdkmanager)
- [Eclipse Temurin JDK (Adoptium)](https://adoptium.net/)
- [Android Command-Line Tools](https://developer.android.com/studio#command-line-tools-only)

---

### 1. Prerequisites: Install Java JDK

Android development requires a **Java JDK** (Java Development Kit). There are multiple sources to obtain the JDK:

| Source | Description | Download |
|--------|-------------|----------|
| **Eclipse Temurin** (formerly AdoptOpenJDK) | Open-source, TCK-certified, free | https://adoptium.net/ |
| **Oracle JDK** | Official Oracle build, free for development | https://www.oracle.com/java/technologies/downloads/ |
| **Amazon Corretto** | OpenJDK build by AWS, free | https://aws.amazon.com/corretto/ |
| **OpenJDK** | Reference implementation via package manager | `sudo apt install openjdk-21-jdk` (Linux) / `brew install openjdk` (macOS) |

> **Which JDK version?** — The recommended LTS version for Android development is **JDK 21**. The guide below uses **Eclipse Temurin JDK 21** as the example. If you prefer a different provider or version, adjust the download URLs accordingly.

We will place the JDK in a user-controlled folder `~/Documents/java_sdk` for portability and consistency.

---

<details open>
<summary>🐧 Linux (Ubuntu/Debian)</summary>

```bash
# 1. Create the java_sdk folder
mkdir -p ~/Documents/java_sdk

# 2. Download the latest Eclipse Temurin JDK 21 LTS (.tar.gz)
curl -L -o /tmp/jdk.tar.gz \
  "https://api.adoptium.net/v3/binary/latest/21/ga/linux/x64/jdk/hotspot/normal/eclipse?project=jdk"

# 3. Extract into ~/Documents/java_sdk (strip the top-level folder so contents go directly into java_sdk)
tar -xzf /tmp/jdk.tar.gz -C ~/Documents/java_sdk --strip-components=1

# 4. Clean up
rm /tmp/jdk.tar.gz

# 5. Verify
~/Documents/java_sdk/bin/java -version
~/Documents/java_sdk/bin/javac -version
```

**Set JAVA_HOME and PATH** — Add the following lines to `~/.bashrc` (or `~/.zshrc` if using Zsh):

```bash
echo 'export JAVA_HOME="$HOME/Documents/java_sdk"' >> ~/.bashrc
echo 'export PATH="$JAVA_HOME/bin:$PATH"' >> ~/.bashrc
```

> If you're using Zsh, replace `~/.bashrc` with `~/.zshrc`.

Apply the changes:

```bash
source ~/.bashrc   # or source ~/.zshrc
```

Verify:

```bash
java -version
javac -version
echo "JAVA_HOME=$JAVA_HOME"
```

</details>

<details>
<summary>🪟 Windows (PowerShell as Admin)</summary>

```powershell
# 1. Create the java_sdk folder (if it doesn't exist)
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\Documents\java_sdk"

# 2. Download the latest Eclipse Temurin JDK 21 LTS (.zip)
Invoke-WebRequest -Uri "https://api.adoptium.net/v3/binary/latest/21/ga/windows/x64/jdk/hotspot/normal/eclipse?project=jdk" -OutFile "$env:TEMP\jdk.zip"

# 3. Extract into %USERPROFILE%\Documents\java_sdk
Expand-Archive -Path "$env:TEMP\jdk.zip" -DestinationPath "$env:USERPROFILE\Documents\java_sdk" -Force

# 4. If the contents are inside a subfolder (e.g., jdk-21.*), move them up one level:
$extracted = Get-ChildItem "$env:USERPROFILE\Documents\java_sdk" -Directory | Select-Object -First 1
if ($extracted -and $extracted.Name -like "jdk*") {
    Get-ChildItem -Path "$($extracted.FullName)" | Move-Item -Destination "$env:USERPROFILE\Documents\java_sdk" -Force
    Remove-Item -Path "$($extracted.FullName)" -Force
}

# 5. Clean up
Remove-Item "$env:TEMP\jdk.zip" -Force

# 6. Verify
& "$env:USERPROFILE\Documents\java_sdk\bin\java" -version
& "$env:USERPROFILE\Documents\java_sdk\bin\javac" -version
```

**Set JAVA_HOME and PATH** — Run these commands in PowerShell (as Admin) to set user-level environment variables:

```powershell
# Set JAVA_HOME (user-level environment variable)
setx JAVA_HOME "$env:USERPROFILE\Documents\java_sdk"

# Add %JAVA_HOME%\bin to PATH (user-level)
setx PATH "$env:PATH;$env:USERPROFILE\Documents\java_sdk\bin"
```

Restart PowerShell, then verify:

```powershell
java -version
javac -version
echo "JAVA_HOME=$env:JAVA_HOME"
```

</details>

<details>
<summary>🍎 macOS</summary>

```bash
# 1. Create the java_sdk folder
mkdir -p ~/Documents/java_sdk

# 2. Download the latest Eclipse Temurin JDK 21 LTS (.tar.gz)
curl -L -o /tmp/jdk.tar.gz \
  "https://api.adoptium.net/v3/binary/latest/21/ga/mac/x64/jdk/hotspot/normal/eclipse?project=jdk"

# 3. Extract into ~/Documents/java_sdk (strip the top-level folder)
tar -xzf /tmp/jdk.tar.gz -C ~/Documents/java_sdk --strip-components=1

# 4. Clean up
rm /tmp/jdk.tar.gz

# 5. Verify
~/Documents/java_sdk/bin/java -version
~/Documents/java_sdk/bin/javac -version
```

**Set JAVA_HOME and PATH** — Add the following lines to `~/.zshrc`:

```bash
echo 'export JAVA_HOME="$HOME/Documents/java_sdk"' >> ~/.zshrc
echo 'export PATH="$JAVA_HOME/bin:$PATH"' >> ~/.zshrc
```

Apply the changes:

```bash
source ~/.zshrc
```

Verify:

```bash
java -version
javac -version
echo "JAVA_HOME=$JAVA_HOME"
```

> **Note for Apple Silicon (M1/M2/M3) Macs**: The URL above fetches the x64 build. For native ARM build, replace `mac/x64` with `mac/aarch64` in the URL:
> ```
> https://api.adoptium.net/v3/binary/latest/21/ga/mac/aarch64/jdk/hotspot/normal/eclipse?project=jdk
> ```

</details>

---

### 2. Install Android SDK

#### 2.1 Create the Android SDK root folder

<details open>
<summary>🐧 Linux / 🍎 macOS</summary>

```bash
mkdir -p ~/Documents/android_sdk
```

</details>

<details>
<summary>🪟 Windows</summary>

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\Documents\android_sdk"
```

</details>

#### 2.2 Download Android Command-Line Tools

Download the latest Command-Line Tools from Google's official repository to your **Downloads** folder.

> **Latest version (as of 2026)**: `commandlinetools-*-14742923_latest.zip`

<details open>
<summary>🐧 Linux</summary>

```bash
curl -L -o ~/Downloads/cmdline-tools.zip \
  "https://dl.google.com/android/repository/commandlinetools-linux-14742923_latest.zip"
```

</details>

<details>
<summary>🪟 Windows (PowerShell)</summary>

```powershell
Invoke-WebRequest -Uri "https://dl.google.com/android/repository/commandlinetools-win-14742923_latest.zip" -OutFile "$env:USERPROFILE\Downloads\cmdline-tools.zip"
```

</details>

<details>
<summary>🍎 macOS</summary>

```bash
curl -L -o ~/Downloads/cmdline-tools.zip \
  "https://dl.google.com/android/repository/commandlinetools-mac-14742923_latest.zip"
```

</details>

#### 2.3 Extract and arrange folder structure

The downloaded ZIP contains a `cmdline-tools/` folder. It must be restructured so that the tools reside in `android_sdk/cmdline-tools/latest/`.

<details open>
<summary>🐧 Linux</summary>

```bash
# Create a temporary extraction folder
mkdir -p ~/Downloads/cmdline-tools-extract

# Extract the ZIP
unzip -q ~/Downloads/cmdline-tools.zip -d ~/Downloads/cmdline-tools-extract

# Create the target folder structure
mkdir -p ~/Documents/android_sdk/cmdline-tools

# Move the extracted cmdline-tools into "latest/"
mv ~/Downloads/cmdline-tools-extract/cmdline-tools ~/Documents/android_sdk/cmdline-tools/latest

# Clean up
rm -rf ~/Downloads/cmdline-tools.zip ~/Downloads/cmdline-tools-extract
```

</details>

<details>
<summary>🪟 Windows (PowerShell)</summary>

```powershell
# Extract to a temp folder
Expand-Archive -Path "$env:USERPROFILE\Downloads\cmdline-tools.zip" -DestinationPath "$env:USERPROFILE\Downloads\cmdline-tools-extract" -Force

# Create target folder structure
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\Documents\android_sdk\cmdline-tools"

# Move the extracted cmdline-tools into "latest/"
Move-Item -Path "$env:USERPROFILE\Downloads\cmdline-tools-extract\cmdline-tools" -Destination "$env:USERPROFILE\Documents\android_sdk\cmdline-tools\latest"

# Clean up
Remove-Item "$env:USERPROFILE\Downloads\cmdline-tools.zip" -Force
Remove-Item "$env:USERPROFILE\Downloads\cmdline-tools-extract" -Recurse -Force
```

</details>

<details>
<summary>🍎 macOS</summary>

```bash
# Create a temporary extraction folder
mkdir -p ~/Downloads/cmdline-tools-extract

# Extract the ZIP
unzip -q ~/Downloads/cmdline-tools.zip -d ~/Downloads/cmdline-tools-extract

# Create the target folder structure
mkdir -p ~/Documents/android_sdk/cmdline-tools

# Move the extracted cmdline-tools into "latest/"
mv ~/Downloads/cmdline-tools-extract/cmdline-tools ~/Documents/android_sdk/cmdline-tools/latest

# Clean up
rm -rf ~/Downloads/cmdline-tools.zip ~/Downloads/cmdline-tools-extract
```

</details>

#### 2.4 Use sdkmanager to install packages

Now use the just-extracted `sdkmanager` to install and sync the `cmdline-tools;latest` package into the Android SDK root. The `--sdk_root` flag tells sdkmanager where the Android SDK is located. We also auto-accept licenses using `yes |`.

<details open>
<summary>🐧 Linux / 🍎 macOS</summary>

```bash
# Go to the android_sdk root folder
cd ~/Documents/android_sdk

# Install the cmdline-tools;latest package into the Android SDK root (auto-accept licenses)
yes | ./cmdline-tools/latest/bin/sdkmanager --sdk_root="$HOME/Documents/android_sdk" "cmdline-tools;latest"
```

</details>

<details>
<summary>🪟 Windows (PowerShell)</summary>

```powershell
# Go to the android_sdk root folder
cd "$env:USERPROFILE\Documents\android_sdk"

# Install the cmdline-tools;latest package into the Android SDK root (auto-accept licenses)
echo y | .\cmdline-tools\latest\bin\sdkmanager.bat --sdk_root="$env:USERPROFILE\Documents\android_sdk" "cmdline-tools;latest"
```

</details>

> **What this does**: It uses the cmdline-tools to download and install the "cmdline-tools;latest" SDK package into your Android SDK root (`android_sdk`), ensuring you have the latest version installed in the correct location.

---

### 3. Set Environment Variables

Set `ANDROID_HOME` (and the deprecated alias `ANDROID_SDK_ROOT`) and add the important SDK tool directories to `PATH`.

<details open>
<summary>🐧 Linux</summary>

Add to `~/.bashrc` (or `~/.zshrc`):

```bash
echo 'export ANDROID_HOME="$HOME/Documents/android_sdk"' >> ~/.bashrc
echo 'export ANDROID_SDK_ROOT="$ANDROID_HOME"' >> ~/.bashrc
echo 'export PATH="$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$ANDROID_HOME/emulator:$PATH"' >> ~/.bashrc
```

> If you're using Zsh, replace `~/.bashrc` with `~/.zshrc`.

Apply:

```bash
source ~/.bashrc   # or source ~/.zshrc
```

</details>

<details>
<summary>🪟 Windows</summary>

Run these commands in PowerShell (as Admin) to set user-level environment variables:

```powershell
# Set ANDROID_HOME
setx ANDROID_HOME "$env:USERPROFILE\Documents\android_sdk"

# Set ANDROID_SDK_ROOT (legacy alias)
setx ANDROID_SDK_ROOT "$env:USERPROFILE\Documents\android_sdk"

# Add tool directories to PATH (user-level)
setx PATH "$env:PATH;$env:USERPROFILE\Documents\android_sdk\cmdline-tools\latest\bin;$env:USERPROFILE\Documents\android_sdk\platform-tools;$env:USERPROFILE\Documents\android_sdk\emulator"
```

Restart PowerShell, then verify.

</details>

<details>
<summary>🍎 macOS</summary>

Add to `~/.zshrc`:

```bash
echo 'export ANDROID_HOME="$HOME/Documents/android_sdk"' >> ~/.zshrc
echo 'export ANDROID_SDK_ROOT="$ANDROID_HOME"' >> ~/.zshrc
echo 'export PATH="$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$ANDROID_HOME/emulator:$PATH"' >> ~/.zshrc
```

Apply:

```bash
source ~/.zshrc
```

</details>

---

### 4. Verify Installation

> **Important**: If you opened a **new terminal** after setting the environment variables above, run `source ~/.bashrc` (Linux) or `source ~/.zshrc` (macOS) first to load them in the current session. On Windows, restart PowerShell.

#### 4.1 List installed packages

After setting environment variables (step 3), `sdkmanager` is on your PATH and knows the SDK root from `$ANDROID_HOME`.

```bash
# Linux / macOS / Windows
sdkmanager --list_installed
```

You should see output listing the installed SDK packages (e.g., `cmdline-tools;latest`). ✅

#### 4.2 Accept licenses

```bash
sdkmanager --licenses
```

Type `y` to accept all license agreements when prompted.

#### 4.3 Verify Java and Android environment

```bash
# Check Java
java -version
javac -version
echo "JAVA_HOME=$JAVA_HOME"

# Check Android SDK
echo "ANDROID_HOME=$ANDROID_HOME"
echo "ANDROID_SDK_ROOT=$ANDROID_SDK_ROOT"
```

---

## 2. Setup iOS SDK for Local Development

### Prerequisites

<!-- TODO: Add prerequisites for iOS SDK setup -->
- _Content to be provided_

### Installation Steps

<!-- TODO: Add iOS SDK installation steps -->
- _Content to be provided_

### Environment Variables

<!-- TODO: Add environment variables configuration for iOS SDK -->
- _Content to be provided_

### Verification

<!-- TODO: Add steps to verify iOS SDK installation -->
- _Content to be provided_