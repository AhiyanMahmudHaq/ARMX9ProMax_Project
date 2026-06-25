# ARMX9ProMax_Project
Firstly, I must state that: 
### *1. I am not responsible for any damage such as "bricking" that is caused to your TV box or any damage to your health caused by soldering.*
### *2. You must figure out the hardware specifications of your TV box and then decide whether you can use this repository can help you install the Armbian GNU/Linux distribution.*
The ARMX9ProMax Project is a repository containing the files required to flash and run Armbian GNU/Linux on a generic, unbranded TV box that shows up as "TX9PROMAX" in AIDA64 and other device information apps inside of the OEM/stock Android distribution. The small, Allwinner H616-powered budget TV box is very commonly found in TV box stores in Bangladesh. This repository contains the Device Tree Source and the Device Tree Blob as well as the optimal Linux kernel for ONLY this specific model of TV box.
This project is protected under the GNU General Public License 3.0 to promote free and open source software.
The typical device specifications for the "TX9PROMAX" TV box are given below in the form of a table:

<img width="589" height="595" alt="image" src="https://github.com/user-attachments/assets/dec3186d-e769-4f6c-8f75-f1cb081f734d" />

This project requires extraction of the Device Tree Blob (DTB/.dtb) files from the running Android system, if root is present in it through wired debugging via Android Debug Bridge (ADB). Wired debugging is not possible using a generic USB-A-to-USB-C data transfer enabled cable; you will need a specialized USB-A-to-USB-A cable which can be made by joining two cables with USB-A by soldering. However, this is not recommended for absolute beginners as they may damage the cable. **Always be careful when soldering to avoid burns. Never breathe in solder fumes.**

Important links to documentation of the installation process as a whole:

[Wikipedia link to what a Device Tree is](https://en.wikipedia.org/wiki/Devicetree)

[Wikipedia link to what chainloading is](https://en.wikipedia.org/wiki/Chain_loading)

[Wikipedia link to Universal Serial Bus (USB) On-The-Go (OTG) cable, which is required for wired debugging](https://en.wikipedia.org/wiki/USB_On-The-Go)

[Linux kernel support for Allwinner H616](https://linux-sunxi.org/H616)

[Android Open Source Project (AOSP) about the DTBO partition (Android 9)](https://source.android.com/docs/core/architecture/dto/compile)

