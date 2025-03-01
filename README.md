# My Programming Tutor - Android App

## Table of Contents
- [Introduction](#introduction)
- [Setup and Installation](#setup-and-installation)
  - [Prerequisites](#prerequisites)
  - [Android SDK Configuration](#android-sdk-configuration)
  - [Building the Project](#building-the-project)
- [Android SDK Management](#android-sdk-management)
  - [Adding Additional SDK Components](#adding-additional-sdk-components)
  - [Viewing Installed SDK Components](#viewing-installed-sdk-components)
  - [Updating SDK Components](#updating-sdk-components)
- [Emulator Setup and Configuration](#emulator-setup-and-configuration)
  - [Installing Emulator Components](#installing-emulator-components)
  - [Creating an AVD (Android Virtual Device)](#creating-an-avd-android-virtual-device)
  - [Running the Emulator](#running-the-emulator)
- [Project Structure](#project-structure)
- [Development](#development)
  - [Dynamic Feature Modules](#dynamic-feature-modules)
- [Testing](#testing)
- [Common Issues and Solutions](#common-issues-and-solutions)

## Introduction

MyProgrammingTutor is an Android application designed to help users learn programming concepts through interactive tutorials and exercises.

## Setup and Installation

### Prerequisites

- Java Development Kit (JDK) 11 or higher
- Git for version control
- Internet connection for downloading dependencies

### Android SDK Configuration

The project is configured to use the Android SDK located at `/workspaces/android-sdk` in the Codespaces environment. If you're working on a different environment, follow these steps to set up the Android SDK:

#### For Codespaces Environment:

The Android SDK has been set up at `/workspaces/android-sdk` with the following components:
- Android Platform Tools (adb, fastboot)
- Android SDK Platform 34 (Android 14)
- Android SDK Build Tools 34.0.0

If you need to reconfigure the Android SDK in Codespaces, you can run the following commands:

```bash
# Create directory for Android SDK
mkdir -p /workspaces/android-sdk

# Download Android command-line tools
cd /workspaces/android-sdk
wget https://dl.google.com/android/repository/commandlinetools-linux-9477386_latest.zip

# Extract the tools
unzip commandlinetools-linux-9477386_latest.zip
mkdir -p cmdline-tools
mv cmdline-tools latest
mkdir -p cmdline-tools
mv latest cmdline-tools/

# Accept licenses and install SDK components
export ANDROID_HOME=/workspaces/android-sdk
yes | cmdline-tools/latest/bin/sdkmanager --licenses
cmdline-tools/latest/bin/sdkmanager "platform-tools" "platforms;android-34" "build-tools;34.0.0"

# Update local.properties file
echo "sdk.dir=/workspaces/android-sdk" > local.properties
```

#### For Local Development (Windows, macOS, Linux):

1. Download and install Android Studio from [developer.android.com](https://developer.android.com/studio)
2. During installation, Android Studio will install the Android SDK
3. Update the `local.properties` file in the project root to point to your SDK location:
   ```
   sdk.dir=C\:\\Users\\YourUsername\\AppData\\Local\\Android\\Sdk   # Windows
   # OR
   sdk.dir=/Users/YourUsername/Library/Android/sdk                  # macOS
   # OR
   sdk.dir=/home/YourUsername/Android/Sdk                          # Linux
   ```

### Building the Project

To build the project, run:

```bash
./gradlew assembleDebug
```

To install the app on a connected device or emulator:

```bash
./gradlew installDebug
```

## Android SDK Management

### Adding Additional SDK Components

To add more SDK components, use the `sdkmanager` tool:

```bash
# Set up environment variable pointing to the Android SDK
export ANDROID_HOME=/workspaces/android-sdk

# List available packages
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager --list

# Install specific components (examples)
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager "platforms;android-33"  # Android 13
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager "build-tools;33.0.2"    # Build tools
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager "extras;google;google_play_services"  # Google Play services
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager "ndk;25.2.9519653"      # NDK
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager "cmake;3.22.1"          # CMake for NDK projects
```

### Viewing Installed SDK Components

To see which components are currently installed:

```bash
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager --list_installed
```

### Updating SDK Components

To update all installed components to their latest versions:

```bash
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager --update
```

## Emulator Setup and Configuration

### Installing Emulator Components

The emulator component and x86_64 system image have already been installed:

```bash
# Set the Android SDK environment variable
export ANDROID_HOME=/workspaces/android-sdk

# Install the emulator package
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager "emulator"

# Install the x86_64 system image with Google APIs for Android 34
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager "system-images;android-34;google_apis;x86_64"
```

To verify your installed system images:
```bash
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager --list_installed | grep system-images
```

### Creating an AVD (Android Virtual Device)

Now that you have the system image installed, create an Android Virtual Device:

```bash
# First check available device definitions
$ANDROID_HOME/cmdline-tools/latest/bin/avdmanager list device

# Create a new AVD named "pixel6_api34" using the x86_64 system image
$ANDROID_HOME/cmdline-tools/latest/bin/avdmanager create avd \
    --name "pixel6_api34" \
    --package "system-images;android-34;google_apis;x86_64" \
    --device "pixel_6"
```

### Running the Emulator in Codespaces

Since Codespaces is a cloud environment with no direct display, you need to set up a VNC server to view the emulator GUI:

#### Option 1: Headless Testing (No GUI)

For automated testing without needing to see the emulator screen:

```bash
# Start the emulator in headless mode
$ANDROID_HOME/emulator/emulator -avd pixel6_api34 -no-window -no-audio -no-boot-anim &

# Check that the emulator is running
$ANDROID_HOME/platform-tools/adb devices
```

#### Option 2: Using VNC for GUI Access

For visual access to the emulator:

1. Install VNC server and noVNC:

```bash
sudo apt update
sudo apt install -y tigervnc-standalone-server novnc
```

2. Set up the VNC server:

```bash
# Create VNC directory and set password
mkdir -p ~/.vnc
echo "password" | vncpasswd -f > ~/.vnc/passwd
chmod 600 ~/.vnc/passwd

# Start VNC server
vncserver :1 -geometry 1366x768 -depth 24
```

3. Start noVNC (web-based VNC client):

```bash
/usr/share/novnc/utils/launch.sh --vnc localhost:5901 --listen 6080 &
```

4. Forward port 6080 in Codespaces by clicking on the "PORTS" tab in the bottom panel, then click on the "Forward a Port" button and enter 6080.

5. Start the emulator:

```bash
# Set DISPLAY to the VNC server and start the emulator
DISPLAY=:1 $ANDROID_HOME/emulator/emulator -avd pixel6_api34 -gpu swiftshader_indirect &
```

6. Access the emulator by clicking on the globe icon next to port 6080 in the "PORTS" tab, or navigate to the URL shown.

### Emulator Management Commands

Useful commands for working with the emulator:

```bash
# List all AVDs
$ANDROID_HOME/cmdline-tools/latest/bin/avdmanager list avd

# List running emulators
$ANDROID_HOME/platform-tools/adb devices

# Delete an AVD
$ANDROID_HOME/cmdline-tools/latest/bin/avdmanager delete avd --name "pixel6_api34"

# Install APK to emulator
$ANDROID_HOME/platform-tools/adb install app/build/outputs/apk/debug/app-debug.apk
```

### Testing Your App with the Emulator

Once your emulator is running:

1. Build your debug APK:
```bash
./gradlew assembleDebug
```

2. Install the APK on the emulator:
```bash
$ANDROID_HOME/platform-tools/adb install app/build/outputs/apk/debug/app-debug.apk
```

3. Launch your app using its package name:
```bash
$ANDROID_HOME/platform-tools/adb shell am start -n com.example.myprogrammingtutor/.MainActivity
```

## Project Structure

The project follows the standard Android project structure:

- `app/` - Main application module
  - `src/main/` - Main source set
    - `java/` - Kotlin/Java source files
    - `res/` - Resources (layouts, strings, etc.)
    - `AndroidManifest.xml` - App manifest
- `app/dynamicfeature/` - Dynamic feature module
- `gradle/` - Gradle wrapper files
- `build.gradle` - Project-level build file
- `app/build.gradle` - App-specific build file
- `local.properties` - Local SDK configuration

## Development

### Dynamic Feature Modules

This project uses Android's Dynamic Feature Modules to deliver features on-demand. The `dynamicfeature` module demonstrates this functionality.

To work with dynamic features:

1. Ensure the base app implements the Play Core library
2. Request feature installation in your code:
   ```kotlin
   val manager = SplitInstallManagerFactory.create(context)
   val request = SplitInstallRequest.newBuilder()
       .addModule("dynamicfeature")
       .build()
   manager.startInstall(request)
   ```

## Testing

The project includes both unit tests and instrumented tests:

- Unit tests: `./gradlew test`
- Instrumented tests: `./gradlew connectedAndroidTest`

## Common Issues and Solutions

### Android SDK Path Issues

If you encounter SDK path issues, ensure your `local.properties` file points to the correct location.

### Emulator Performance

For better emulator performance:
- Use x86/x86_64 system images when possible
- Enable hardware acceleration (HAXM for Intel, WHPX for Windows, KVM for Linux)
- Increase RAM allocation for the AVD

### License Issues

If you see license errors when downloading SDK components:
```bash
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager --licenses
```
Accept all licenses when prompted.

### Emulator Specific Issues

#### Error: KVM is required to run this AVD
In Codespaces, KVM might not be available. Use the `-gpu swiftshader_indirect` option as shown above.

#### Cannot connect to the emulator
Try resetting the ADB server:
```bash
$ANDROID_HOME/platform-tools/adb kill-server
$ANDROID_HOME/platform-tools/adb start-server
```

#### Slow emulator performance
In Codespaces, emulator performance may be limited. Consider:
- Reducing emulator window size and resolution
- Using the `-no-boot-anim` flag
- Increasing RAM allocation for your Codespace
- For testing only, consider using Firebase Test Lab or other cloud testing services