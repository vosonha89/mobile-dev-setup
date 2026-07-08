# mobile-dev-setup

A comprehensive guide for setting up local development environment for mobile development, including Android SDK and iOS SDK configurations.

## Table of Contents

- [Setup Android SDK for Local Development](#1-setup-android-sdk-for-local-development)
  - [1. Install Java JDK](#1-prerequisites-install-java-jdk)
  - [2. Install Android SDK](#2-install-android-sdk)
  - [3. Install Gradle](#3-install-gradle)
  - [4. Set Environment Variables](#4-set-environment-variables)
  - [5. Verify Installation](#5-verify-installation)
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

### 3. Install Gradle

Gradle is the build system used by Android. While most Android projects ship a Gradle wrapper (`./gradlew`), a standalone Gradle install is useful for `gradle init`, IDE references, and as a system-wide fallback.

📖 **References**:
- [Gradle Releases](https://gradle.org/releases/)
- [Gradle Installation Guide](https://gradle.org/install/)

We will place Gradle in a user-controlled folder `~/Documents/gradle` for portability and consistency (matching the layout of `java_sdk` and `android_sdk`).

> **Which version?** — This guide uses **Gradle 9.6.1** as the example (compatible with latest React Native). Check [gradle.org/releases](https://gradle.org/releases/) for the latest stable version and update the URL below if needed.

#### 3.1 Download Gradle

<details open>
<summary>🐧 Linux / 🍎 macOS</summary>

```bash
curl -L -o ~/Downloads/gradle.zip \
  "https://services.gradle.org/distributions/gradle-9.6.1-bin.zip"
```

</details>

<details>
<summary>🪟 Windows (PowerShell)</summary>

```powershell
Invoke-WebRequest -Uri "https://services.gradle.org/distributions/gradle-9.6.1-bin.zip" -OutFile "$env:USERPROFILE\Downloads\gradle.zip"
```

</details>

#### 3.2 Extract Gradle

<details open>
<summary>🐧 Linux / 🍎 macOS</summary>

```bash
# Create the gradle folder
mkdir -p ~/Documents/gradle

# Extract into ~/Documents/gradle (strip the top-level folder so contents go directly into gradle)
unzip -q ~/Downloads/gradle.zip -d ~/Documents/gradle-temp
mv ~/Documents/gradle-temp/gradle-9.6.1/* ~/Documents/gradle/
rm -rf ~/Documents/gradle-temp ~/Downloads/gradle.zip
```

</details>

<details>
<summary>🪟 Windows (PowerShell)</summary>

```powershell
# Create the gradle folder
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\Documents\gradle"

# Extract to a temp folder
Expand-Archive -Path "$env:USERPROFILE\Downloads\gradle.zip" -DestinationPath "$env:USERPROFILE\Documents\gradle-temp" -Force

# Move extracted contents up one level into the gradle folder
$extracted = Get-ChildItem "$env:USERPROFILE\Documents\gradle-temp" -Directory | Select-Object -First 1
if ($extracted) {
    Get-ChildItem -Path "$($extracted.FullName)" | Move-Item -Destination "$env:USERPROFILE\Documents\gradle" -Force
    Remove-Item -Path "$($extracted.FullName)" -Force
}

# Clean up
Remove-Item "$env:USERPROFILE\Downloads\gradle.zip" -Force
Remove-Item "$env:USERPROFILE\Documents\gradle-temp" -Recurse -Force
```

</details>

#### 3.3 Verify

<details open>
<summary>🐧 Linux / 🍎 macOS</summary>

```bash
~/Documents/gradle/bin/gradle --version
```

</details>

<details>
<summary>🪟 Windows (PowerShell)</summary>

```powershell
& "$env:USERPROFILE\Documents\gradle\bin\gradle.bat" --version
```

</details>

You should see Gradle, Kotlin, and JVM version info printed. ✅

> **Note**: For per-project builds, prefer the **Gradle Wrapper** (`./gradlew` / `gradlew.bat`). Each Android project pins its own Gradle version in `gradle/wrapper/gradle-wrapper.properties`. The standalone install above is useful for `gradle init` and as a system-wide fallback.

---

### 4. Set Environment Variables

Set `ANDROID_HOME` (and the deprecated alias `ANDROID_SDK_ROOT`) and add the important SDK tool directories to `PATH`. We also set `GRADLE_HOME` and add Gradle's `bin` directory to `PATH`.


<details open>
<summary>🐧 Linux</summary>

Add to `~/.bashrc` (or `~/.zshrc`):

```bash
echo 'export ANDROID_HOME="$HOME/Documents/android_sdk"' >> ~/.bashrc
echo 'export ANDROID_SDK_ROOT="$ANDROID_HOME"' >> ~/.bashrc
echo 'export PATH="$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$ANDROID_HOME/emulator:$PATH"' >> ~/.bashrc
echo 'export GRADLE_HOME="$HOME/Documents/gradle"' >> ~/.bashrc
echo 'export PATH="$GRADLE_HOME/bin:$PATH"' >> ~/.bashrc
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

# Set GRADLE_HOME and add Gradle to PATH
setx GRADLE_HOME "$env:USERPROFILE\Documents\gradle"
setx PATH "$env:PATH;$env:USERPROFILE\Documents\gradle\bin"
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
echo 'export GRADLE_HOME="$HOME/Documents/gradle"' >> ~/.zshrc
echo 'export PATH="$GRADLE_HOME/bin:$PATH"' >> ~/.zshrc
```

Apply:

```bash
source ~/.zshrc
```

</details>

---

### 5. Verify Installation

> **Important**: If you opened a **new terminal** after setting the environment variables above, run `source ~/.bashrc` (Linux) or `source ~/.zshrc` (macOS) first to load them in the current session. On Windows, restart PowerShell.

#### 5.1 List installed packages

After setting environment variables (step 4), `sdkmanager` is on your PATH and knows the SDK root from `$ANDROID_HOME`.

```bash
# Linux / macOS / Windows
sdkmanager --list_installed
```

You should see output listing the installed SDK packages (e.g., `cmdline-tools;latest`). ✅

#### 5.2 Accept licenses

```bash
sdkmanager --licenses
```

Type `y` to accept all license agreements when prompted.

#### 5.3 Verify development environment

```bash
# Check Java
java -version
javac -version
echo "JAVA_HOME=$JAVA_HOME"

# Check Android SDK
echo "ANDROID_HOME=$ANDROID_HOME"
echo "ANDROID_SDK_ROOT=$ANDROID_SDK_ROOT"

# Check Gradle
gradle --version
echo "GRADLE_HOME=$GRADLE_HOME"
```

---

## 2. Setup iOS SDK for Local Development

> 🍎 macOS-only — the iOS SDK ships with Xcode, which is distributed exclusively
>   for macOS via the App Store and developer.apple.com.

📖 **References**:
- [Xcode (Official)](https://developer.apple.com/xcode/)
- [Command Line Tools for Xcode](https://developer.apple.com/download/all/)
- [xcode-install (Fastlane)](https://github.com/fastlane/xcode-install)
- [CocoaPods](https://cocoapods.org/)

---

### 1. Prerequisites

| Requirement | Details |
|-------------|---------|
| **macOS host** | Apple Silicon or Intel Mac. Xcode 26.3 requires **macOS 16 Tahoe**; Xcode 26.0/26.1 also run on **macOS 15 Sequoia**. |
| **Apple ID** | Free Apple ID is sufficient for the SDK, simulators, and code signing. A paid **Apple Developer Program** membership ($99/yr) is only required to deploy to a physical device or publish to the App Store / TestFlight. |
| **Disk space** | ~50 GB free (Xcode ~15 GB + simulators ~10-20 GB + tooling). |
| **Admin access** | Required to install system components and accept Xcode licenses. |

---

### 2. Install Command Line Tools for Xcode (CLT)

The Command Line Tools are a lightweight (~1 GB) prerequisite that provides `git`, `clang`, `make`, and other essential build tools. Install them **before** Xcode.

<details open>
<summary>Method A — Interactive GUI prompt (simplest)</summary>

```bash
xcode-select --install
```

A pop-up dialog will appear. Click **Install** and wait for the download to complete.

</details>

<details>
<summary>Method B — Scriptable via `softwareupdate`</summary>

```bash
# 1. Check what's already installed
softwareupdate --history | grep -i "command line tools"

# 2. Install (or update) Command Line Tools
sudo softwareupdate -i "Command Line Tools for Xcode-26.3"
```

> Replace `26.3` with the exact version string shown by `softwareupdate --list`.

</details>

**Verify CLT installation:**

```bash
xcode-select -p
# Expected: /Library/Developer/CommandLineTools

clang --version
# Expected: Apple clang version 17.x (or similar)
```

If `xcode-select -p` returns an error, re-run the install command above.

---

### 3. Install Xcode 26.3

Xcode is the full IDE (~15-35 GB) that contains the iOS SDK, simulators, and all development tools.

<details open>
<summary>Method A — Mac App Store (simplest, GUI)</summary>

```bash
# Open the App Store directly to the Xcode page
open "macappstore://apps.apple.com/app/xcode/id497799835"
```

Click **Get / Install** and wait for the download to complete. Xcode will be placed in `/Applications/Xcode.app` by default.

</details>

<details>
<summary>Method B — `xcode-install` gem (scriptable, CI-friendly)</summary>

```bash
# 1. Install xcode-install via Homebrew
brew install xcode-install

# 2. Install Xcode 26.3 (downloads + installs automatically)
xcversion install 26.3

# 3. (Optional) List all installed Xcode versions
xcversion list
```

`xcode-install` automatically renames the app to `/Applications/Xcode-26.3.app`, allowing multiple Xcode versions to coexist. Use `xcversion select 26.3` to switch the active version.

</details>

> **Note**: Xcode 27 is currently in **BETA** (announced at WWDC 2026). If you want to preview the next major release, download it from [developer.apple.com/download](https://developer.apple.com/download/all/). For production development, stick with **Xcode 26.3** (stable).

---

### 4. Select Active Developer Directory

After installing Xcode, tell the system which version to use:

```bash
# Point the active developer directory to Xcode 26.3
sudo xcode-select -s /Applications/Xcode-26.3.app/Contents/Developer

# Accept the Xcode license agreement (required for CLI tools)
sudo xcodebuild -license accept

# Run first-launch setup (installs additional components)
xcodebuild -runFirstLaunch
```

**Verify:**

```bash
xcode-select -p
# Expected: /Applications/Xcode-26.3.app/Contents/Developer
```

---

### 5. Install iOS Simulators

Simulators let you test iOS apps on your Mac without a physical device.

```bash
# 1. List currently installed simulator runtimes
xcrun simctl list runtimes

# 2. Download the latest iOS platform (e.g., iOS 26)
xcodebuild -downloadPlatform iOS

# 3. Create a new simulator device
xcrun simctl create "iPhone 17 Pro" "iPhone 17 Pro" "com.apple.CoreSimulator.SimRuntime.iOS-26-0"

# 4. List available devices to confirm
xcrun simctl list devices available
```

**GUI alternative:** Open Xcode → **Settings** → **Platforms** / **Devices** to browse and download additional simulator runtimes (iOS 26, iOS 25, watchOS, tvOS, etc.).

---

### 6. (Optional) Install iOS Tooling

These tools are commonly needed for iOS/React Native development but are not strictly required for the SDK itself.

#### 6.1 Homebrew (package manager)

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Add Homebrew to your `PATH` as instructed by the installer, then run `brew doctor` to verify.

#### 6.2 Ruby via rbenv (recommended)

macOS's system Ruby is outdated and fragile. Use `rbenv` to manage a modern Ruby version (≥ 3.0) for CocoaPods and Fastlane:

```bash
# Install rbenv + ruby-build
brew install rbenv ruby-build

# Initialize rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(rbenv init -)"' >> ~/.zshrc
source ~/.zshrc

# Install Ruby 3.3.x (or latest stable)
rbenv install 3.3.6
rbenv global 3.3.6

# Verify
ruby --version
# Expected: ruby 3.3.x
```

#### 6.3 CocoaPods (dependency manager for iOS projects)

```bash
# Via Homebrew (preferred)
brew install cocoapods

# Or via gem (requires rbenv Ruby)
gem install cocoapods

# Verify
pod --version
```

#### 6.4 Fastlane (automation for iOS builds)

```bash
brew install fastlane

# Verify
fastlane --version
```

#### 6.5 Watchman (file watcher, recommended for React Native)

```bash
brew install watchman

# Verify
watchman --version
```

#### 6.6 ios-deploy (deploy to physical device from CLI)

```bash
npm i -g ios-deploy

# Verify
ios-deploy --version
```

---

### 7. Set Environment Variables

Add the following to `~/.zshrc` (or `~/.bash_profile` on older macOS versions):

```bash
# iOS / Xcode developer toolchain
export DEVELOPER_DIR="/Applications/Xcode-26.3.app/Contents/Developer"
export PATH="$DEVELOPER_DIR/usr/bin:$DEVELOPER_DIR/Toolchains/XcodeDefault.xctoolchain/usr/bin:$PATH"

# rbenv (if installed in step 6.2)
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
```

**Apply the changes:**

```bash
source ~/.zshrc
```

> **Note**: We expose the `Contents/Developer` toolchain subfolder rather than the `.app` bundle itself. `.app` directories are macOS application bundles and cannot be added directly to `PATH`.

---

### 8. Verify Installation

Run the following commands to confirm everything is set up correctly:

```bash
# Xcode version
xcodebuild -version
# Expected: Xcode 26.3, Build version …

# Active developer directory
xcode-select -p
# Expected: /Applications/Xcode-26.3.app/Contents/Developer

# Swift compiler
swift --version
# Expected: Swift 6.x

# Clang compiler
clang --version
# Expected: Apple clang 17.x

# Available simulators
xcrun simctl list devices available

# Optional tooling (if installed)
pod --version          # CocoaPods 1.16+
fastlane --version     # fastlane 2.22x
watchman --version     # watchman 2026.x
```

If all commands return version info without errors, your iOS SDK setup is complete. ✅
