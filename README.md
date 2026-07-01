# The Hisillicon TX9 Pro Max Project

## **Disclaimer**
* **I am not responsible for any of the damages that may be inflicted to your device.** 
* **The incorrect installation of an operating system (OS) on an embedded computer system may render it permanently unuseable, often referred to as *"bricking."***
* **Never violate the End User License Agreement of a manufacturer's device, *violations include reverse engineering the source code of properietary software and uploading it to the internet or using it for commercial purposes.***
* **If users intend to install their own system software on such devices, they must analyze the device's hardware specifications and think critically before attempting to do so.**

## Preface
The Hisillicon TX9 Pro Max Project's GitHub repository is dedicated to the development of a custom build of the Armbian Trixie GNU/Linux distribution (minimal or server variant), referred onwards as Armbian GNU/Linux, for the use of a cheap, common TV box that goes by the name of 'TX9 Pro Max.' Since the device is an open-source, white-label product; many different third-party Chinese companies resell it, so I did not specify its brand (the company that sells it). The TV box is found in television (TV) box and video accessories stores across Bangladesh. Although the boxes claim highly performative device hardware specifications (e.g., 256 Gigabytes of on-device non-volatile memory, 16 Gigabytes of Random Access Memory, Allwinner H313 System on a Chip (SoC)) the TV box comprises of significantly less performative device hardware specifications, as given below:

<img width="589" height="601" alt="Screenshot from 2026-06-24 13-32-07" src="https://github.com/user-attachments/assets/946d4ac7-df1b-49bc-a556-c56b798f5e4e" />

I initially began developing this project to learn more about embedded Linux systems, as I aspire to work professionally with embedded and IoT systems. The project includes the important documentation of the Linux kernel, running on the Allwinner H616 SoC. Allwinner H616 (sun50iw9p1) is a SoC that features a Quad-Core Cortex-A53 ARM CPU, and a Mali-G31 MP2 GPU from ARM. It is targeted for low-end TV boxes that require hardware accelerated transcoding capability for media streaming, usually of popular media streaming platforms like YouTube (e.g., Tanix TX9, Tanix TX9 Pro (original), TX9 Pro Max (non-specific brand; counterfeit model), X96, X96Q, etcetera). [Source](linux-sunxi.org/H616)

This repository contains the full procedure of research, development and installation of the custom build of Armbian GNU/Linux, on the H616-powered machine. It even includes the mistakes that were made during the entire process. The whole project, including all the files are protected as *open-source* and is provided with *absolutely* no warranty, to the extent the law provides. Therefore, you do not require crediting the owner.

## Research

The primary resource for learning how to build the customized variant of Armbian GNU/Linux for this exact TV box is given below:
Note: This has been taken from the internet and abridged by using a Large Language Model (LLM), so it may contain mistakes caused by hallucination. The link of the thread where it has been taken from: [en.eeworld.com.cn/bbs/thread-1245672-1-1.html](https://en.eeworld.com.cn/bbs/thread-1245672-1-1.html)

Markdown
# Linux Sunxi (Allwinner H616 / MQ-Quad) Build and Boot Guide

## 1. U-Boot Build and Boot from USB

### Install Dependencies and Pull Source
bash
# Install sunxi-fel tools and Arm Trusted Firmware (arm64)
# Pull down the latest U-Boot
Modify U-Boot Configuration
For the MQ-Quad, there is no Ethernet interface.
    1. Execute make menuconfig.
    2. Turn off network support on the homepage: [ ] Networking support.
    3. Use the AXP313 power management chip by modifying the driver file: drivers/power/axp305.c.
Compile and Boot U-Boot
Bash
# Compile U-Boot
# Boot from USB using sunxi-fel
../sunxi-tools/sunxi-fel uboot u-boot-sunxi-with-spl.bin
2. Linux Kernel Compilation
Download Mainline Kernel
Bash
git clone git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
# If speed is slow, download the mainline kernel tarball directly from [https://kernel.org](https://kernel.org)
tar -xvzf linux-6.0-rc3.tar.gz
Note: Because the 6.0 kernel is too new and many drivers do not support it, you can alternatively use the stable version of the kernel for driver porting from the H616 mainline maintainer:
Bash
git clone -b h616-v13 [https://github.com/apritzel/linux](https://github.com/apritzel/linux)
Modify the Device Tree
File: arch/arm64/boot/dts/allwinner/sun50i-h616-orangepi-zero2.dts (or mq-quad.dts)
Because the 3.3v of MQ-Quad is not connected to PMIC for control, it is necessary to add a Fixed 3.3v node. Add reg_vcc3v3 under the reg_vcc5v node (otherwise the IO cannot be initialized, resulting in serial port failure).
DTS
reg_vcc3v3: vcc3v3 { 
    compatible = "regulator-fixed"; 
    regulator-name = "vcc-3v3"; 
    regulator-min-microvolt = <3300000>; 
    regulator-max-microvolt = <3300000>; 
    regulator-always-on; 
};
Modify reg_aldo1 in the pio node to reg_vcc3v3:
DTS
&pio { 
    vcc-pc-supply = <&reg_vcc3v3>; 
    vcc-pf-supply = <&reg_vcc3v3>; 
    vcc-pg-supply = <&reg_vcc3v3>; 
    vcc-ph-supply = <&reg_vcc3v3>; 
    vcc-pi-supply = <&reg_vcc3v3>; 
}; 
Modify mmc0's vmmc-supply (reg_dcdce) to reg_vcc3v3 (otherwise the sunxi-mmc driver cannot start):
DTS
&mmc0 { 
    vmmc-supply = <&reg_vcc3v3>; 
    cd-gpios = <&pio 5 6 GPIO_ACTIVE_LOW>; /* PF6 */ 
    bus-width = <4>; 
    status = "okay"; 
};
Compile the Kernel, DTB, and Modules
Bash
# Use the default configuration
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- defconfig 

# Compile the kernel image
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j8 Image

# Compile device tree
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j8 dtbs 

# Compile modules (optional, used after making the file system)
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j8 modules 
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- INSTALL_MOD_PATH=modules modules_install
