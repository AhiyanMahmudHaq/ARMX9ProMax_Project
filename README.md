# ARMX9ProMax_Project
Firstly, I must state that: 
### *1. I am not responsible for any damage such as "bricking" that is caused to your TV box.*
### *2. You must figure out the hardware specifications of your TV box and then decide whether you can use this repository can help you install the Armbian GNU/Linux distribution.*
The ARMX9ProMax Project is a repository containing the files required to flash and run Armbian GNU/Linux on a generic, unbranded TV box that shows up as "TX9PROMAX" in AIDA64 and other device information apps inside of the OEM/stock Android distribution. The small, Allwinner H616-powered budget TV box is very commonly found in TV box stores in Bangladesh. This repository contains the Device Tree Source and the Device Tree Blob as well as the optimal Linux kernel for ONLY this specific model of TV box.
This project is protected under the GNU General Public License 3.0 to promote free and open source software.
The typical device specifications for the "TX9PROMAX" TV box are given below in the form of a table:

<img width="590" height="600" alt="image" src="https://github.com/user-attachments/assets/8755e24c-24a6-47a6-a3e6-34990e06298e" />

This project requires extraction of Device Tree Blob (DTB/.dtb) files from the running Android system, if root is present in it.

Important links to documentation of the Allwinner H616 found on the motherboard/mainboard:

[Wikipedia link to what a Device Tree is](https://en.wikipedia.org/wiki/Devicetree)

[Linux kernel support for Allwinner H616](https://linux-sunxi.org/H616)

[Android Open Source Project about the DTBO partition (Android 9)](https://source.android.com/docs/core/architecture/dto/compile)

