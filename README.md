# Creating a Static Kernel Driver for BMP280 on Raspberry Pi

## Introduction

A static kernel driver is built directly into the Linux kernel, unlike a loadable kernel module (LKM), which is inserted dynamically. When built statically, the driver is always available and does not need to be loaded manually. This document explains how to integrate the `bmp280temp_driver` into the Raspberry Pi kernel statically and includes debugging tips for smooth operation.

## Steps to Create a Static Kernel Driver

### 1. Preparing the Kernel Source

To include the `bmp280temp_driver` in the kernel source tree:

#### Navigate to the kernel source directory:
```sh
cd ~/linux
```

#### Move your driver source file to the correct directory:
```sh
mv ~/bmp280temp_driver.c drivers/misc/
```

#### Modify the Kconfig file to make the driver selectable during kernel configuration:
```sh
nano drivers/misc/Kconfig
```

Add the following lines:
```sh
config BMP280TEMP
    bool "BMP280 Temperature Sensor Driver"
    depends on I2C
    help
      Enable support for the BMP280 temperature sensor via I2C.
```

#### Modify the Makefile to compile the driver:
```sh
nano drivers/misc/Makefile
```

Append:
```sh
obj-y += bmp280temp_driver.o
```

### 2. Configuring and Compiling the Kernel

#### Configure the kernel to include the driver statically:
```sh
make menuconfig
```

- Navigate to `Device Drivers → Misc Devices`
- Find `BMP280 Temperature Sensor Driver`
- Select `[ * ]` to compile it statically (instead of `M` for a module)

#### Build the kernel and modules:
```sh
make -j4 Image modules dtbs
```

#### Install the new kernel:
```sh
sudo make modules_install
sudo make install
```

#### Reboot to apply changes:
```sh
sudo reboot
```

### 3. Verifying the Driver

#### Check if the driver is present in the kernel:
```sh
dmesg | grep bmp280
```

If successfully loaded, you should see log messages related to `bmp280temp_driver`.

#### Check if the driver is registered under I2C:
```sh
ls /sys/bus/i2c/drivers/
```

Expected output:
```sh
bmp280temp_driver
```

#### Read sensor data (if applicable):
```sh
cat /sys/class/hwmon/hwmonX/temp1_input
```

### 4. Debugging & Common Issues

#### 1. Probe Function Failing with `-22`
- Ensure the I2C address matches the sensor’s actual address (`0x76` or `0x77`).
- Check if `i2c_add_driver()` is correctly registering the driver.
- Debug with:
```sh
dmesg | tail -20
```

#### 2. Driver Not Appearing in `/sys/bus/i2c/drivers/`
- Confirm the `i2c_driver` structure has a valid `.probe` function.
- Ensure the kernel tree has been rebuilt and installed correctly.

#### 3. Checking Kernel Configurations
- Verify the kernel includes the driver statically:
```sh
zgrep BMP280 /proc/config.gz
```

Expected output:
```sh
CONFIG_BMP280=y
CONFIG_BMP280_I2C=y
```

## Conclusion

By following these steps, the `bmp280temp_driver` will be built directly into the Linux kernel, ensuring it loads automatically at boot. Debugging steps help resolve common issues, making it easier to integrate I2C-based sensor drivers in an embedded Linux environment.

