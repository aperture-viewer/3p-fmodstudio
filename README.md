# Aperture Viewer - 3p-fmodstudio

This repository contains the necessary scripts to package the proprietary FMOD Studio API for use with the Aperture Viewer's `autobuild` build system.

> [!IMPORTANT]
> **This repository is for project maintainers only.**
>
> If you are a developer simply building Aperture Viewer from source, you **do not** need to use this repository. The main viewer's `autobuild install` command will automatically download the correct, pre-packaged FMOD library from the releases page of this repository.
>
> The process outlined below is only necessary when a new version of the FMOD Studio API is released and needs to be packaged for distribution with the viewer.

## Purpose

The sole purpose of this repository is to take the official FMOD Studio API installer (e.g., an `.exe` file) and package it into a `.tar.bz2` archive that the `autobuild` system can consume. The final archive is then hosted as a GitHub Release artifact.

## Prerequisites

Before you begin, you will need:
*   A free developer account on the [FMOD website](https://www.fmod.com/).
*   The **FMOD Studio API** installer for Windows (e.g., `fmodstudioapiXXXXwin-installer.exe`). Make sure you download the **API**, not the FMOD Studio Tool.
*   A working `autobuild` environment, as set up for the main Aperture Viewer repository.
*   The `dos2unix` utility installed in your Cygwin/bash environment.

## Instructions for Packaging a New FMOD Version

This is a one-time process for each new version of FMOD that needs to be integrated.

### Step 1: Prepare the Repository

1.  Clone this repository to your local machine.
2.  Download the required FMOD Studio API installer from the FMOD website.
3.  Place the downloaded FMOD installer (the `.exe` file) into the root of this repository's directory.

### Step 2: Configure the Build Script

1.  Open the `build-cmd.sh` script in a text editor (e.g., Notepad++).
2.  Update the `FMOD_VERSION` and `FMOD_VERSION_PRETTY` variables at the top of the file to match the version you just downloaded.
    *   `FMOD_VERSION`: A compact, no-dots version number (e.g., `20227` for 2.02.27).
    *   `FMOD_VERSION_PRETTY`: The full version number string (e.g., `2.02.27`).
3.  Save the `build-cmd.sh` file.

### Step 3: Ensure Correct Line Endings

The `build-cmd.sh` script must have Unix-style (LF) line endings to run correctly in the bash environment.

1.  Open a Cygwin, Git Bash, or other bash-compatible terminal.
2.  Navigate to this repository's directory.
3.  Run the following command:
    ```sh
    dos2unix build-cmd.sh
    ```

### Step 4: Build and Package with Autobuild

1.  Open a standard **Windows Command Prompt (`cmd.exe`)**.
2.  Navigate to this repository's directory.
3.  Run the `autobuild` commands to build and then package the library:
    ```cmd
    autobuild build -A 64 --all
    autobuild package -A 64 --results-file result.txt
    ```
    *   During the `build` step, the FMOD installer will run. You may need to grant it administrative permissions.
    *   After the `package` step, you will have a `fmodstudio-....tar.bz2` file in the directory and a `result.txt` file containing the MD5 hash.

### Step 5: Create a GitHub Release

1.  Go to the main page of this repository on GitHub.
2.  Click on **"Releases"** in the right-hand sidebar, then **"Create a new release"**.
3.  For the "Tag version", enter the FMOD version (e.g., `v2.02.27`).
4.  In the release description, you can note what version of FMOD this is.
5.  In the "Attach binaries" section, upload the `fmodstudio-....tar.bz2` archive you created in the previous step.
6.  Publish the release.

### Step 6: Update Aperture Viewer

This is the final step to make the new FMOD package available to the main viewer build.

1.  Go to the new release page you just created.
2.  Right-click on the `.tar.bz2` asset link and select **"Copy Link Address"**.
3.  Open the `result.txt` file from your local repository and copy the MD5 hash value.
4.  In your main **Aperture-Viewer** source code repository, open the `autobuild.xml` file.
5.  Find the `<installable name="fmodstudio">` section and update the `<url>` and `<hash>` tags with the values you just copied.
6.  Commit and push the change to `autobuild.xml`.

The process is now complete. All subsequent builds of Aperture Viewer will automatically download and use this new version of FMOD.

## License and Copyright

The `build-cmd.sh` script is a simple build utility. The FMOD Studio API is proprietary software provided by Firelight Technologies Pty Ltd. and is subject to its own license, which you agree to when downloading it from their website. This repository does not contain or redistribute the FMOD software itself, only the tools to package it for our build system.
