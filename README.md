## Note before starting
I only tested this on M1 pro.

## Prerequisites

Before starting, ensure that you have the following installed on your system:

- **Homebrew**: You can install Homebrew using the following command:
  ```bash
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
  ```

- **Qt5**: Install the required Qt5 version via Homebrew:
  ```bash
  brew install qt@5
  ```

- **Other Required Tools**:
  ```bash
  brew install cmake wget
  ```

## Compilation Steps

### 1. Clone the VESC Tool and BLDC repositories

```bash
mkdir ~/Documents/Work/Motors
cd ~/Documents/Work/Motors
git clone https://github.com/vedderb/vesc_tool.git
git clone https://github.com/vedderb/bldc.git
```

### 2. Set up the Qt Path

To ensure the correct version of Qt5 is used, add it to your PATH:

```bash
echo 'export PATH="/opt/homebrew/opt/qt@5/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 3. Install ARM GCC Toolchain

You need to install the **GCC ARM toolchain** for building the firmware:

- **Option 1: Install via Homebrew (newer versions)**
  ```bash
  brew install --cask gcc-arm-embedded
  ```

- **Option 2: Download the recommended version manually**:
  ```bash
  cd ~/Documents/Work/Motors/bldc
  wget https://firmware.vesc-project.com/gcc-arm-none-eabi-7-2018-q2-update-mac.tar.bz2 -P downloads/
  tar -xjf downloads/gcc-arm-none-eabi-7-2018-q2-update-mac.tar.bz2 -C tools/
  ```

### 4. Set the `FWPATH` in `build_cp_fw`

Navigate to the **`vesc_tool`** directory and update the `build_cp_fw` script to point to the `bldc` directory:

```bash
nano ~/Documents/Work/Motors/vesc_tool/build_cp_fw
```

Update the `FWPATH` line as follows:

```bash
FWPATH="../bldc"
```

### 5. Build the Firmware

Now build the firmware and generate the necessary resources for the VESC Tool:

```bash
cd ~/Documents/Work/Motors/vesc_tool
./build_cp_fw
```

### 6. Build the VESC Tool

To compile the VESC Tool using **Qt5**, create a build directory and run the following commands:

```bash
mkdir ~/Documents/Work/Motors/vesc_tool/vesc_tool.build
cd ~/Documents/Work/Motors/vesc_tool/vesc_tool.build
qmake ../vesc_tool/vesc_tool.pro
make -j8
```

### 7. Create a DMG (macOS only)

To create a DMG for the VESC Tool (for easier distribution):

```bash
../tools/Qt/5.15.2/clang_64/bin/macdeployqt VESC\ Tool.app -dmg -qmldir=../../vesc_tool
```

### 8. Ad-Hoc Code Signing

Since macOS requires code signing, you need to sign the compiled `.app` file:

```bash
sudo codesign --force --deep --sign - /path/to/VESC\ Tool.app
```

> **Note**: Replace `/path/to/VESC\ Tool.app` with the correct path to the VESC Tool you built.

### 9. Running the VESC Tool

After signing, you can run the application directly by opening the `.app` file or by clicking on the DMG file if you created it.

If you encounter the warning about firmware compatibility, proceed with caution as this is a test version of the VESC Tool.

---

### Troubleshooting

- If you see errors related to **code signing**, ensure you sign the app properly using the `codesign` command.
- If macOS still blocks the app from running, you might need to disable **Gatekeeper** temporarily using:
  ```bash
  sudo spctl --master-disable
  ```

Don't forget to re-enable Gatekeeper once the installation is complete:
```bash
sudo spctl --master-enable
```

---
