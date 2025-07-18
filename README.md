# Aperture Viewer - FMOD Studio Packaging Tool (`3p-fmodstudio`)

This repository provides the necessary script to package the proprietary FMOD Studio API for use with the Aperture Viewer's `autobuild` build system.

> [!IMPORTANT]
> **Developer Action Required for FMOD Support**
>
> Due to FMOD licensing, Aperture Viewer cannot redistribute the FMOD Studio libraries directly. To build Aperture Viewer with FMOD audio support, you **must** follow the one-time setup process below.
>
> This process creates a local package of the FMOD libraries that the Aperture Viewer build system will use.

## Purpose

The FMOD Studio API is distributed by Firelight Technologies as a developer SDK installer (e.g., an `.exe` file). The Aperture Viewer build system, however, consumes dependencies as pre-packaged archives (e.g., a `.tar.bz2` file).

The `build-cmd.sh` script in this repository is a utility that automates the process of unpacking the FMOD installer and re-packaging the necessary library files into an `autobuild`-compatible archive.

## Prerequisites

Before you begin, you will need:
*   A free developer account on the [FMOD website](https://www.fmod.com/).
*   The **FMOD Studio API** installer for Windows (e.g., `fmodstudioapiXXXXwin-installer.exe`). Make sure you download the **API**, not the FMOD Studio Tool.
*   A working `autobuild` environment, as set up for the main Aperture Viewer repository.
*   A text editor capable of saving with Unix-style line endings, such as Notepad++ or Visual Studio Code.

## One-Time Setup Instructions

Follow these steps once to set up your local environment.

### Step 1: Prepare the Repository

1.  Clone this repository (`aperture-3p-fmodstudio`) to a permanent location on your machine, for example: `E:\aperture-3p-fmodstudio`.
2.  Download the required FMOD Studio API installer from the FMOD website.
3.  Place the downloaded FMOD installer (the `.exe` file) into the root of this repository's directory (`E:\aperture-3p-fmodstudio`).

### Step 2: Configure the Build Script

1.  Open `build-cmd.sh` in a text editor (e.g., Notepad++).
2.  At the top of the file, update the `FMOD_VERSION` and `FMOD_VERSION_PRETTY` variables to **exactly match the version you just downloaded**.

    *   `FMOD_VERSION`: A compact, no-dots version number (e.g., `20227` for 2.02.27).
    *   `FMOD_VERSION_PRETTY`: The full version number string (e.g., `2.02.27`).

3.  Save the `build-cmd.sh` file.

### Step 3: Set Correct Line Endings (CRITICAL STEP)

The `build-cmd.sh` script is run in a `bash` environment which requires Unix-style line endings (LF), not Windows-style (CRLF). Your text editor must be set to save the file in the correct format.

**Instructions for Notepad++:**
1.  With `build-cmd.sh` open, look at the status bar at the bottom right of the Notepad++ window.
2.  It will likely say **"Windows (CR LF)"**. You must change this.
3.  Go to the menu: `Edit > EOL Conversion`.
4.  Select **`UNIX (LF)`**.
5.  The text in the status bar should now say **"Unix (LF)"**.
6.  Now, **save the file** again to apply the change.

> [!WARNING]
> If you skip this step, the build in the next step will fail with a script error.

### Step 4: Build and Package the FMOD Libraries

1.  Open a standard **Windows Command Prompt (`cmd.exe`)**.
2.  Navigate to this repository's directory (e.g., `cd /d E:\aperture-3p-fmodstudio`).
3.  Run the following `autobuild` commands:
    ```cmd
    autobuild build -A 64 --all
    autobuild package -A 64 --results-file result.txt
    ```
    *   The `build` command will launch the FMOD installer. You **must allow it administrative permissions** to proceed. It will install the SDK into a temporary location.
    *   The `package` command will then find the installed files and create a `fmodstudio-VERSION-windows64-BUILDID.tar.bz2` archive in the current directory.
    *   `result.txt` will also be created, containing the MD5 hash of the new archive.

### Step 5: Final Placement

1.  Create a dedicated, permanent directory on your machine for pre-built third-party dependencies. We strongly recommend `C:\aperture\3p_packages\`.
2.  Copy the newly created `fmodstudio-....tar.bz2` file into that directory.

Your local environment is now set up. The main Aperture Viewer build system is pre-configured to look for this package in this location.

## License and Copyright

The `build-cmd.sh` script is a simple build utility. The FMOD Studio API is proprietary software provided by Firelight Technologies Pty Ltd. and is subject to its own license, which you agree to when downloading from their website. This repository does not contain or redistribute the FMOD software itself.
