# LUM Build Overview

## 📘 Introduction
LUM (Logical Unit Module) is a software bundle used to flash or update target partitions in
embedded systems such as automotive infotainment units. It includes system images, bootloaders,
firmware, partition maps, Android kernel files, and metadata required for deployment.


---

## 📁 Directory Structure
ivi_cicd_bitbake_build-main is the main project folder containing everything related to the IVI CI/CD BitBake build system.

![LUM-folder-structure.png](https://github.com/ullas8431/test-LUM/blob/main/LUM-folder-structure.png)

## 📁 buildsystem/
The heart of the build system. Contains scripts, configurations, and BitBake layers.

### 🔹 bin/
Contains executable scripts or helper tools used during the build process.
### 🔹 build/
Working directory where builds are executed.
  -  conf/: Contains core BitBake config files like:
  -  local.conf: Local build settings.
  -  bblayers.conf: Lists active layers for the build.
### 🔹 build_configs/
Platform-specific build configurations.
- COMMON/: Shared settings across all builds.
- HCP3/, OIA/: Specific hardware or platform targets.
### 📦 BitBake Layers (meta-eso_*)
Each layer represents a modular part of the build process.

### 🔹 meta-eso_aosp/
Integrates with Android Open Source Project.
- conf/: Layer configuration.
- recipes/: BitBake recipes for AOSP components.
### 🔹 meta-eso_build/
Core build logic.
- classes/: Custom BitBake classes.
- conf/: Layer config.
- lib/: Python or shell libraries.
- recipes/: Build instructions for packages.
### 🔹 meta-eso_finalize/
Final steps in the build process.
- conf/, recipes/: Final packaging, cleanup, or post-processing.
### 🔹 meta-eso_image/
Image creation logic.
- classes/, conf/, recipes/: Defines how the final image is built.
### 🔹 meta-eso_image-preparation/
Prepares the image (e.g. bootloader setup).
- conf/, recipes/
### 🔹 meta-eso_install/
Handles installation logic.
- classes/, conf/, recipes/
### 🔹 meta-eso_packaging/
Manages packaging of build artifacts.
- classes/, conf/, recipes/
### 🔹 meta-eso_prepare/
Pre-build preparation steps.
- conf/, recipes/
### 🔹 meta-eso_processing/
Post-build processing.
- conf/, recipes/

----
### 🐍 pylib/
Contains Python utilities used in the build system.

- Common/: Shared Python modules.
- misc/: Miscellaneous utilities.
  - helpers/: Helper functions.
  -  parser/: Parsing logic.
  - storage/: Data storage or caching logic.
### ⚙️ variant_configs/
- Contains configuration files for different build variants (e.g., LUM, IFU).
- Each .conf file defines settings for a specific variant.
### 🔧 Tools
- cariad-synctool: Likely a tool for syncing code or artifacts with Cariad systems.
- synctool: General sync utility.

---
## 🔄 Build Workflow
The build process is defined as a custom BitBake task `pkg_LUM`, which runs after initialization and before the main build.

### Key Steps:
- Copy system images and metadata
- Include bootloader binaries and kernel images
- Add project-specific binaries (e.g., safety-island for OIA)
- Include DSP firmware and exception lists
- Copy partition layout files
- Package Android kernel images
- Integrate manufacturing and UCC tools (These are needed for Android boot flow validation and fastboot flashing.)
- Add signing utilities and autorunner scripts
- Run target parser for IOC configurations
- Create minimal ext4 filesystem for HUD (These are likely required to re-sign key partitions before deployment.)
- Archive all files into a `.tgz` package

---





## 🧭 Getting Started with the LUM Build 

### 🔹 1. Understand What LUM Is
LUM (Logical Unit Module) is a packaging system that bundles everything needed to flash or update target partitions. It includes:
  - Kernel, DTBs, system image (sys.ext4)
  - Bootloaders (SBL, LK, t-base, etc.)
  - DSP firmware
  - Safety binaries (for OIA)
  - Partition mapping files
  - Android kernel images
  - Signature tools
  - Target descriptions for each IOC
 
 ### 🔹 2. Key Build Scripts
📄 run.sh
 - Sets up environment variables and paths.
 - Triggers build.sh with parameters like pkg-LUM.

📄 build.sh
 - Parses parameters and sets up the BitBake environment.
 - Dynamically prepares local.conf using global and variant configs.

### 🔹 3. What Happens During a LUM Build
The do_pkg_LUM() task performs:

- 1. **Collect System Artifacts**
  - sys.ext4, Image, dtb.img, etc.

- 2. **Copy Bootloaders**
  - From IMAGE_DATA/bootloaders/
    
- 3. **Add Hypervisor Images**
  - From IMAGE_DATA/hypervisor/
    
- 4. **Include Safety Binaries**
  - Only for OIA project

- 5. **Add DSP Firmware**
   - abox_firmware_evt1.bin
- 6. **Include Partition Layout Files**
  - From engineering (ufs0lun*.txt)
- 7. **Add Android Kernel Images**
  - boot.img, vbmeta.img, etc.
- 8. **Include Manufacturing Tools**
  - mnfctool/, ucctool/
- 9. **Add Signature Tools**
  -  Project-specific signing scripts
- 10.  **Include Metadata**
  -  partnumbers.json
- 11. **Create Dummy EXT4 Image**
  - hud_basic.ext4
- 12. **Run IOC Target Parser**
  - Generates per-IOC layout and metadata
- 13. **Final Packaging**
  - Creates *_images.tgz with all content

-------
## 🔹 4. Final Output
The final LUM package (*_images.tgz) contains:

- System images (sys.ext4, sys.ext4.metadata, sys_roothashsigned.bin, Image, dtb.img, dtbo.img)
- Bootloaders (lk.bin, t-base.img, etc.)
- Android kernel :  boot.img, vbmeta.img, etc.
- DSP firmware : abox_firmware_evt1.bin
- Partition maps : ufs0lun*.txt
- Safety binaries (if applicable)
- Signature tools and metadata: partnumbers.json, exceptionlist.txt
- Dummy image: hud_basic.ext4
- Manufacturing tools: mnfctool/, ucctool/


-----







