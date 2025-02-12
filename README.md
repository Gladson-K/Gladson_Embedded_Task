# ADC18TEMP BMP280 Temperature Sensor Driver

## Introduction
This repository contains a **dynamically loadable kernel module (LKM)** for interfacing with the **BMP280 temperature sensor** via the I2C bus on a **Raspberry Pi 5**. The driver provides a character device interface for reading temperature data and communicating with the sensor.

## Features
- Implements an I2C-based character driver for the **BMP280** sensor.
- Provides temperature readings using **sysfs**.
- Supports **polling** with configurable intervals.
- Dynamically loadable as a kernel module (**LKM**), allowing insertion and removal without recompiling the kernel.

## Prerequisites
Ensure your Raspberry Pi has the necessary development tools installed:
```bash
sudo apt update && sudo apt install -y raspberrypi-kernel-headers build-essential i2c-tools
```
Enable I2C on the Raspberry Pi using **raspi-config**:
```bash
sudo raspi-config
```
- Navigate to `Interfacing Options` > `I2C` and enable it.
- Reboot the system.

Verify that the sensor is detected on the I2C bus:
```bash
i2cdetect -y 1
```
You should see an entry for **0x76** or **0x77** (depending on the BMP280 module).

## Building the Driver
Compile the driver using:
```bash
make
```
This generates the `adc18temp_driver.ko` file, which can be inserted into the kernel.

## Loading the Driver
To insert the module into the kernel, run:
```bash
sudo insmod adc18temp_driver.ko
```
Check if the module is loaded:
```bash
dmesg | tail -20
ls /sys/bus/i2c/drivers/
```
You should see logs confirming that the driver has registered successfully.

## Creating a Device Node
If the `/dev/adc18temp` device node is not created automatically, create it manually:
```bash
sudo mknod /dev/adc18temp c 245 0
sudo chmod 666 /dev/adc18temp
```
(Note: Replace **245** with the major number assigned to the driver in `dmesg`.)

## Using the Driver
### Reading Temperature Data
You can read temperature data using:
```bash
cat /dev/adc18temp
```
Or using a simple script:
```bash
echo "Temperature: $(cat /dev/adc18temp)°C"
```

## Unloading the Driver
To remove the driver, run:
```bash
sudo rmmod adc18temp_driver
```
Verify removal:
```bash
dmesg | tail -10
lsmod | grep adc18temp
```

## Debugging
If the driver fails to load or work correctly:
- Check `dmesg` logs for errors:
  ```bash
  dmesg | tail -50
  ```
- Ensure the I2C device is detected using `i2cdetect`.
- Verify the correct I2C address is used (default: **0x76** for BMP280).
- Recompile and reload the module.

## License
This project is licensed under the **GPL (GNU General Public License)**.

## Author
**Gladson K** - Developer of the ADC18TEMP BMP280 Driver.

