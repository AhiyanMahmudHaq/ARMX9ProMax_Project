# The Hisillion TX9 Pro Max Project

### **Disclaimer:**
* **I am not responsible for any of the damages that may be inflicted to your device.** 
* **The incorrect installation of an operating system (OS) on an embedded computer system may render it permanently unuseable, often referred to as *"bricking."***
* **Never violate the End User License Agreement of a manufacturer's device, violations include reverse engineering the source code of properietary software and uploading it to the internetor using it for commercial purposes.**

### Preface
The Hisillicon TX9 Pro Max Project's GitHub repository is dedicated to the development of a custom build of Armbian Trixie GNU/Linux (minimal or server variant) for the use of a cheap, common TV box that is found in television (TV) box and video accessories stores across Bangladesh. Although the manufacturers claim highly performative device hardware specifications(e.g., 256 Gigabytes of on-device non-volatile memory, 16 Gigabytes of Random Access Memory, Allwinner H313 System on a Chip (SoC)) the TV box comprises of significantly less performative device hardware specifications, as given below:

<img width="589" height="601" alt="Screenshot from 2026-06-24 13-32-07" src="https://github.com/user-attachments/assets/946d4ac7-df1b-49bc-a556-c56b798f5e4e" />

I initially began developing this project to learn more about embedded Linux systems, as I aspire to work professionally with embedded and IoT systems. The project includes the important documentation of the Linux kernel, running on the Allwinner H616 SoC. Allwinner H616 (sun50iw9p1) is a SoC that features a Quad-Core Cortex-A53 ARM CPU, and a Mali-G31 MP2 GPU from ARM. It is targeted for low-end TV boxes that require hardware accelerated transcoding capability for media streaming, usually of popular media streaming platforms like YouTube (e.g., Tanix TX9, Tanix TX9 Pro (original), TX9 Pro Max (non-specific brand; counterfeit model), X96, X96Q, etcetera). [Source](linux-sunxi.org/H616)

This repository contains the full procedure of research development and installation of Armbian Trixie GNU/Linux (minimal/server) on the H616-powered machine. It even includes the mistakes that were made during the entire process. The whole project, including all the files are protected as *open-source* and is provided with *absolutely* no warranty, to the extent the law provides. Therefore, you do not require crediting the owner.

### Research and development
