## YOCTO PROJECT - RASPBERRY PI5 IMAGE FILES

I have attached the Image Files generated for Raspberry Pi 5 board by utilizing Scarthgap Poky Repository for Building the Raspberry pi 5 image it took 6.5 to 7 Hours for completion of Building (Core-image-minimal)

# Scarthgap-based Raspberry Pi 5 Image Creation

## Overview
This document provides a step-by-step guide to building and testing a **Scarthgap-based Raspberry Pi 5** image using the **Yocto Project**. The guide covers setting up the environment, configuring the build process, compiling the image, and testing it on the Raspberry Pi 5.

---

## 1. Prerequisites
Before you begin, ensure you have the following:

- A **Linux-based system** (Ubuntu 22.04 recommended)
- At least **50GB** of free disk space
- **8GB RAM** (minimum), **16GB+** recommended
- Installed dependencies:
  ```bash
  sudo apt update && sudo apt install -y gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev xterm
  ```
- A **USB drive** (formatted and mounted at `/mnt/yocto-build`) for storing the build files

---

## 2. Setting Up the Yocto Environment
### 2.1 Clone the Required Repositories
```bash
mkdir -p /mnt/yocto-build && cd /mnt/yocto-build
git clone -b scarthgap https://git.yoctoproject.org/poky.git
git clone -b scarthgap https://github.com/agherzan/meta-raspberrypi.git
git clone -b scarthgap https://git.openembedded.org/meta-openembedded
git clone -b scarthgap https://git.yoctoproject.org/meta-security
```

### 2.2 Source the Build Environment
```bash
cd /mnt/yocto-build/poky
source oe-init-build-env ../build
```

---

## 3. Configuring the Build
### 3.1 Modify `local.conf`
Edit `conf/local.conf`:
```bash
nano conf/local.conf
```
Modify the following variables:
```bash
MACHINE = "raspberrypi5"
DISTRO = "poky"
PACKAGE_CLASSES = "package_rpm"
EXTRA_IMAGE_FEATURES = "debug-tweaks"
BB_NUMBER_THREADS = "4"
PARALLEL_MAKE = "-j4"
IMAGE_FSTYPES = "wic.bz2"
INHERIT += "rm_work"
```
Save and exit.

### 3.2 Modify `bblayers.conf`
Edit `conf/bblayers.conf`:
```bash
nano conf/bblayers.conf
```
Ensure the following layers are added:
```bash
BBLAYERS += "/mnt/yocto-build/meta-raspberrypi"
BBLAYERS += "/mnt/yocto-build/meta-openembedded/meta-oe"
BBLAYERS += "/mnt/yocto-build/meta-openembedded/meta-python"
BBLAYERS += "/mnt/yocto-build/meta-security"
```
Save and exit.

---

## 4. Building the Image
Run the following command to start the build:
```bash
bitbake core-image-minimal
```
This process will take **several hours** depending on your system.

---

## 5. Deploying and Testing the Image
### 5.1 Locate the Built Image
Once the build is complete, navigate to the output directory:
```bash
cd /mnt/yocto-build/build/tmp/deploy/images/raspberrypi5/
ls -lh
```
Look for the generated **WIC image**, e.g.:
```bash
core-image-minimal-raspberrypi5.wic.bz2
```

### 5.2 Extract and Flash the Image
Extract the image:
```bash
bzip2 -d core-image-minimal-raspberrypi5.wic.bz2
```
Flash it to a microSD card (replace `/dev/sdX` with the correct device):
```bash
sudo dd if=core-image-minimal-raspberrypi5.wic of=/dev/sdX bs=4M status=progress && sync
```

### 5.3 Boot the Raspberry Pi 5
1. Insert the microSD card into the Raspberry Pi 5.
2. Power on the device.
3. Access the board via **serial console** or **HDMI output**.

### 5.4 Verify Functionality
#### Check Boot Logs
Run:
```bash
dmesg | less
```
Look for any **errors or warnings** related to hardware.

#### Verify Kernel Modules
```bash
lsmod
```
Ensure essential modules like **I2C, SPI, and GPIO** are loaded.

#### Check Network Connectivity
If using Ethernet:
```bash
ifconfig eth0
ping google.com
```

For Wi-Fi:
```bash
ifconfig wlan0
nmcli device wifi list
```

#### Test File System Integrity
```bash
ls /
ls /home/root
```
Ensure necessary directories exist.

### 5.5 Optional: Enable SSH
```bash
systemctl enable ssh
systemctl start ssh
```
Find the IP address:
```bash
ip a
```
Then SSH into the board:
```bash
ssh root@<RaspberryPi-IP>
```

---

## 6. Troubleshooting
### 6.1 Build Errors
- **Fetch Error** → Check your internet connection and retry.
- **Bitbake failure** → Run:
  ```bash
  bitbake -c cleansstate core-image-minimal
  bitbake core-image-minimal
  ```

### 6.2 Boot Issues
- No output on HDMI → Try a different SD card or check power supply.
- Cannot SSH → Ensure network settings are correct.

---

## 7. Conclusion
You have successfully built, deployed, and tested a **Scarthgap-based Raspberry Pi 5 image** using the **Yocto Project**. This guide covers the essential steps, but you can further customize the image by adding packages, configuring services, or modifying the kernel.

For further enhancements, refer to the [Yocto Project Documentation](https://docs.yoctoproject.org/).

---

## 8. References
- [Yocto Project Documentation](https://docs.yoctoproject.org/)
- [Meta-RaspberryPi Layer](https://github.com/agherzan/meta-raspberrypi)


